# 02. Threat Detection with Amazon GuardDuty

> Played both attacker and defender against a deliberately vulnerable web app: chained an SQL injection, a command injection, and AWS credential theft into a real data breach, then verified that GuardDuty detected the intrusion and that S3 Malware Protection caught a planted test file.

**Pathway:** NextWork, Cloud SecOps and Threat Monitoring | Weeks 2-3: Active Threat Monitoring  
**Time taken:** about 2 hours (plus troubleshooting time on deployment)  
**Region:** `us-west-2` (Oregon) — see [Deployment troubleshooting](#deployment-troubleshooting)  
**Billing:** Pay-as-you-go account, no free tier. All resources deleted after testing. GuardDuty and Malware Protection were only enabled for the duration of this lab.

---

## Objective

Examine a realistic AWS-hosted web app compromise from both sides: exploit it as an attacker would, then confirm as the defender that GuardDuty's machine-learning threat detection actually catches the attack. This mirrors the day-to-day work of a Security Analyst or Cloud Security Engineer. Knowing how an attack chain works is what makes a detection's output meaningful.

## Target application

A deployed copy of [`OWASP Juice Shop`](https://owasp.org/www-project-juice-shop/), a web app intentionally built with security flaws for training. It was deployed with a CloudFormation template (infrastructure as code) rather than built by hand, so the focus could stay on the attack and detection, not app setup.

## Template

[`templates/nextwork-guardduty-setup.yaml`](templates/nextwork-guardduty-setup.yaml) is the CloudFormation template used to deploy the environment. **It was provided by [NextWork](https://nextwork.ai)** as part of this project, not authored by me. It's included here because reading, deploying, and troubleshooting it was a core part of the lab, and because it turned out to describe more security engineering than the walkthrough itself covers (see below).

## Architecture

```mermaid
flowchart TD
    subgraph "Web app infrastructure"
        CF[CloudFront distribution] --> EC2[EC2 instance<br/>Juice Shop web server]
        EC2 --> VPC[New VPC + networking]
    end
    S3[(S3 bucket<br/>secret-information.txt)]
    EC2 -->|instance role credentials| S3
    GD[GuardDuty<br/>enabled on deploy]
    EB[EventBridge rule] --> SF[Step Functions<br/>state machine]
    SF -->|iam:PutRolePolicy<br/>deny-all + token cutoff| EC2

    Attacker((Attacker)) -->|1 - SQL injection| CF
    Attacker -->|2 - Command injection| EC2
    EC2 -->|3 - writes stolen creds to<br/>public credentials.json| CF
    Attacker -->|4 - downloads credentials.json| CF
    Attacker -->|5 - CloudShell, profile 'stolen'| S3
    S3 -.->|GetObject, flagged| GD
    GD -->|finding| Defender((Defender / me))
    GD -.->|finding also triggers| EB
```

## Services and tools

| Service | Role |
|---------|------|
| CloudFormation                      | Deployed the 27-resource Juice Shop stack as infrastructure as code    |
| EC2 + Auto Scaling + VPC            | Hosts the web app                                                      |
| CloudFront                          | Public URL / CDN in front of the app                                   |
| S3                                  | Stores the target's "sensitive" file                                   |
| GuardDuty                           | ML-based threat detection across the account                           |
| GuardDuty Malware Protection for S3 | Scans uploaded objects for malware                                     |
| CloudShell                          | Used (as the attacker) to run AWS CLI commands with stolen credentials |

![Osaka deployment template validation failure](screenshots/06-osaka-region-mapping-error.png)

![Stack stuck in ROLLBACK_FAILED](screenshots/07-stack-rollback-failed-error.png)

![Deleting the failed stack with the secure bucket set to Retain](screenshots/08-stack-delete-retain-bucket.png)

---

## Deployment troubleshooting

The CloudFormation deployment failed twice before succeeding, and diagnosing it was as valuable as the rest of the lab.

| Attempt | Region | Failure | Root cause | Fix |
|---------|--------|---------|------------|-----|
| 1 | Osaka (`ap-northeast-3`)        | `Unable to get mapping for RegionMap::ap-northeast-3::CloudFrontPrefixList` | The template's region map doesn't include every AWS Region, and Osaka wasn't in it    | Redeployed in a major region, `us-west-2` |
| 2 | Oregon (`us-west-2`)            | `TheAutoScalingGroup CREATE_FAILED`: 0 of 1 success signals                 | The EC2 instance didn't send `cfn-signal` back to CloudFormation before the timeout     | See below                                 |
| 2 (rollback) | Oregon (`us-west-2`) | `TheSecureBucket DELETE_FAILED`: bucket not empty | During its brief startup, the EC2 instance had already written a file to the S3 bucket, and S3 won't let CloudFormation delete a non-empty bucket | Deleted the stack with **Retain** selected for `TheSecureBucket`, then emptied and deleted that bucket manually in the S3 console |

**What I changed for the next attempt:** under Stack failure options, I set *Rollback on failure* to **Deactivated** (while leaving *Delete newly created resources during a rollback* **Activated**). This step was essential because the default behavior deletes all evidence the moment something fails, which is exactly wrong for troubleshooting. If the Auto Scaling Group failed again, the stack would pause instead of tearing itself down, letting me check the Auto Scaling Group's **Activity** tab and the instance's system log for the real error.

The stack deployed successfully on the retry in `us-west-2`.

![Stack setup: new resources, standard](screenshots/01-stack-create-start.png)

![Template uploaded](screenshots/02-template-upload.png)

![Stack name and details](screenshots/03-stack-name-details.png)

![Stack failure options configured](screenshots/04-stack-failure-options.png)

![Review page with IAM acknowledgement checked](screenshots/05-stack-review-iam-ack.png)

![Stack status CREATE_COMPLETE](screenshots/09-stack-create-complete.png)

![Outputs tab with JuiceShopURL and TheSecureBucket](screenshots/10-outputs-tab.png)

**Why this is worth documenting:** in a real environment, a stuck rollback caused by a resource an app has already written to is a common and confusing failure mode. Knowing to retain the specific resource, clean it up manually, and then disable rollback to capture root cause (instead of just retrying blindly) is a practical incident-response skill, not just a lab exercise.

---

## Attack walkthrough (as the attacker)

| # | Step | Technique | What happened |
|---|------|-----------|---------------|
| 1 | Logged into the admin portal               | **SQL injection**: entered `' or 1=1;--` as the email, random password     | The injected code always evaluates the login query to true, bypassing authentication entirely    |
| 2 | Set the account username                   | **Command injection**: used a JavaScript payload into the Username field   | The app didn't sanitize input, so the server executed the payload instead of storing it as text |
| 3 | —                                          | Payload queried the EC2 **instance metadata service** (`169.254.169.254`) for a session token, then the instance's IAM credentials | This endpoint is normally only reachable from inside the instance. Command injection let the attacker reach it through the web app |
| 4 | —                                          | Payload wrote the stolen credentials to a **public** path: `.../assets/public/credentials.json` | Turned a credential theft into a credential leak anyone with the URL could read |
| 5 | Retrieved the credentials                  | Visited `credentials.json` directly in the browser | Confirmed `AccessKeyId`, `SecretAccessKey`, `Token`, and `Expiration` for the instance role |
| 6 | Opened AWS CloudShell                      | Set environment variables for the Juice Shop URL and S3 bucket name | CloudShell runs under a temporary AWS account ID, separate from the main account |
| 7 | —                                          | `wget` the stolen `credentials.json` into CloudShell, parsed it with `cat` / `jq` | Got the credentials usable from the command line |
| 8 | Configured a new AWS CLI profile, `stolen` | `aws configure set profile.stolen...` using the parsed values | Gave CloudShell the ability to act as the compromised EC2 instance |
| 9 | Stole the data                             | `aws s3 cp s3://.../secret-information.txt . --profile stolen` | Copied the target's sensitive file using the stolen identity |
| 10 | Confirmed the breach                      | `cat secret-information.txt` | Read the file's contents and found a confirmation string proving the data had leaked |

![Juice Shop landing page](screenshots/11-juiceshop-landing-page.png)

![SQL injection string entered in the login email field](screenshots/12-sql-injection-login-field.png)

![Login Admin challenge solved banner](screenshots/13-sql-injection-success-banner.png)

![Admin profile page](screenshots/14-admin-profile-page.png)

![Username showing object Object after the payload ran](screenshots/15-command-injection-object-object.png)

![Exposed credentials.json contents in the browser](screenshots/16-stolen-credentials-json.png)

![CloudShell terminal opened](screenshots/17-cloudshell-first-open.png)

![Environment variables set for the Juice Shop URL and S3 bucket](screenshots/18-cloudshell-env-vars.png)

![wget downloading the stolen credentials.json](screenshots/19-cloudshell-wget-credentials.png)

![Formatted credentials via cat and jq](screenshots/20-cloudshell-cat-jq-credentials.png)

![AWS CLI profile 'stolen' configured from the credentials](screenshots/21-cloudshell-aws-configure-profile.png)

![aws s3 cp copying the secret file with the stolen profile](screenshots/22-cloudshell-s3-cp-secret-file.png)

![Contents of secret-information.txt, confirming the data leak](screenshots/23-cloudshell-secret-file-contents.png)

**Why SQL injection and command injection are different:** SQL injection manipulates a database query (tricking the login check into returning true). Command injection gets the server to execute arbitrary operating-system-level commands. The first undermined authentication; the second gave direct code execution, which is what made stealing IAM credentials possible.

---

## Detection walkthrough (as the defender)

GuardDuty generated a finding within about 15 minutes of the CloudShell activity, with **High** severity.

**Finding:** `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`

| Part of the finding name | Meaning |
|--------------------------|---------|
| `UnauthorizedAccess`                     | Something happened that the actor shouldn't have had rights to do                                                   |
| `IAMUser/InstanceCredentialExfiltration` | Credentials issued to an EC2 instance were used in a way consistent with theft                                      |
| `InsideAWS`                              | The misuse came from a different AWS account than the instance's own, i.e., CloudShell's temporary account          |

**Why CloudShell triggered this:** CloudShell normally inherits the console user's own permissions and isn't flagged. What made this suspicious wasn't CloudShell itself, it was that CloudShell (running under its own temporary account ID) was using an *EC2 instance's* credentials. GuardDuty's anomaly-detection models flagged that mismatch, not the specific `aws s3 cp` command.

**What the finding's detail view showed:**

| Field | Value |
|-------|-------|
| Resource affected | The IAM role attached to the EC2 instance, used to access the S3 bucket |
| Action            | `GetObject` on the secret file                                          |
| Actor             | IP address and approximate location of the CloudShell session           |

![GuardDuty findings list showing the High-severity finding](screenshots/24-guardduty-findings-list.png)

![Finding detail page with resource, action, and actor](screenshots/25-guardduty-finding-detail.png)

A full incident write-up, in SOC report format, is in [`docs/incident-report.md`](docs/incident-report.md).

---

## Discovery: the template includes automated remediation

The project walkthrough never mentioned this, but reading `templates/nextwork-guardduty-setup.yaml` directly shows the stack deploys a working auto-remediation pipeline alongside the vulnerable app:

| Resource | Role |
|----------|------|
| `RemediationRule` (EventBridge) | Listens specifically for `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS` and `.OutsideAWS` findings from GuardDuty |
| `StateMachine` (Step Functions) | On match, calls `iam:PutRolePolicy` against the compromised role |
| Injected deny policy | An explicit `Deny *` on all actions, conditioned on `aws:TokenIssueTime` being before the time GuardDuty last saw the finding |

**What this means in plain terms:** the moment GuardDuty raised the exact finding this lab produced, the stack was already wired to automatically attach a policy to `TheRole` that invalidates every session token issued before that point, cutting off the stolen credentials without anyone needing to intervene. This is the finding type used in this project, so the pipeline should have fired during this exact attack.

I didn't observe this directly while working through the lab, since the walkthrough doesn't call it out. **If you repeat this project, it's worth checking `TheRole`'s inline policies in the IAM console after the GuardDuty finding appears** to confirm the `RevokeOldSessions` policy was attached, and noting the time delay between the finding and the remediation. That would be a stronger piece of evidence than the template alone.

**Why this matters for the portfolio:** this is a working example of GuardDuty integrated with EventBridge and Step Functions for automatic, policy-based credential revocation, a pattern worth understanding (and worth replicating in project 6, the AI-powered S3 remediation project) even though it wasn't something I built myself here.

---

## Extension: GuardDuty Malware Protection for S3

To go beyond the core project, I enabled [Malware Protection for S3](https://docs.aws.amazon.com/guardduty/latest/ug/pricing-malware-protection-for-s3-guardduty.html) on the same bucket and tested it with an [EICAR test file](https://en.wikipedia.org/wiki/EICAR_test_file), a harmless file designed purely to trigger antivirus and malware-scanning tools.

| Step | Result |
|------|--------|
| Enabled Malware Protection on the bucket, default settings (no tagging, new service role) | Enabled successfully |
| Uploaded `EICAR-test-file.txt`                                                            | Upload succeeded     |
| Checked GuardDuty findings                                                                | New finding: **`Object:S3/MaliciousFile`**, correctly identified as the EICAR test signature, not a real virus |

![Malware Protection for S3 enabled](screenshots/26-malware-protection-enabled.png)

![EICAR test file uploaded](screenshots/27-eicar-file-upload.png)

![Object:S3/MaliciousFile finding for the EICAR file](screenshots/28-eicar-finding.png)

This confirms GuardDuty's S3 malware scanning works end to end. An upload triggers a scan, and a detected signature produces a finding, separate from the network/API-based findings covered earlier.

---

## Security takeaways

What this lab demonstrates about defending against this exact chain:

1. **Input sanitization would have stopped both attacks at the source.** Neither the login field nor the username field validated or escaped user input.
2. **The EC2 instance metadata service is a common privilege-escalation target.** Using IMDSv2 token hardening (already partly present here) and restricting what the instance role can do limits the blast radius if command injection happens anyway.
3. **Least privilege on the instance role matters.** The instance role could read the S3 bucket; a role scoped more tightly, or short-lived credentials with narrower permissions, would have limited what the stolen credentials could do.
4. **GuardDuty's value is behavioral, not signature-based, for this finding type.** It didn't know the exact S3 call was malicious, it recognized that an EC2 instance's credentials being used from an unrelated account ID is itself anomalous.
5. **Public-by-default output paths are dangerous.** The command injection payload didn't just steal credentials, it wrote them somewhere publicly reachable. A write to a private path would have required an extra step to exfiltrate.
6. **Good infrastructure controls don't stop an application-layer bug.** The S3 bucket in this template already had encryption at rest (AES256), blocked all public access, and denied any non-TLS connection. None of that mattered, because the attacker never attacked the bucket directly, they attacked the app to get credentials the bucket was configured to trust. Defense in depth means securing every layer, not just the data store.

## Challenges and lessons learned

- **Deployment troubleshooting** (see above) was the most time-consuming part, and the most transferable skill: diagnosing a region-mapping gap, then a stuck rollback caused by a non-empty bucket, then correctly disabling rollback to capture root cause instead of guessing.
- **Understanding why CloudShell looked suspicious** took some digging. It's not CloudShell that's flagged, it's an identity mismatch between the credentials in use and the account running them.
- **Reading the command injection payload line by line** was necessary to actually explain what it did, rather than just pasting it in and watching it work.

## Cleanup

- Deleted the CloudFormation stack (`NextWork-GuardDuty-project-hugh`)
- Emptied and deleted the auto-created `cf-templates-...` bucket and the stack's secure bucket
- Removed `credentials.json` and `secret-information.txt` from CloudShell
- Deleted the downloaded CloudFormation template and EICAR test file from local storage
- Confirmed GuardDuty and Malware Protection stopped incurring charges once the stack was gone  

![CloudFormation stack deletion](screenshots/29-cleanup-stack-delete.png)

![Auto-created template bucket emptied and deleted](screenshots/30-cleanup-s3-template-bucket.png)

![CloudShell with stolen credentials and secret file removed](screenshots/31-cleanup-cloudshell-files-removed.png)

A full list of what each screenshot proves is in [`docs/evidence.md`](docs/evidence.md).


## Skills demonstrated

`SQL injection` · `command injection` · `EC2 instance metadata exploitation` · `credential theft and exfiltration` · `AWS CLI profiles` · `CloudShell` · `GuardDuty finding analysis` · `malware detection` · `infrastructure as code (CloudFormation)` · `CloudFormation deployment troubleshooting`

## Folder contents

```
02-guardduty-threat-detection/
├── README.md
├── templates/
│   └── nextwork-guardduty-setup.yaml
├── screenshots/
└── docs/
    ├── evidence.md
    └── incident-report.md
```