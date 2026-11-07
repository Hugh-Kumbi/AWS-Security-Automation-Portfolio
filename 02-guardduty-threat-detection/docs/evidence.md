# Evidence Index

## CloudFormation deployment

| # | Screenshot | What it proves |
|---|------------|----------------|
| 1 | [`01-stack-create-start.png`](../screenshots/01-stack-create-start.png) | Starting a new stack with "With new resources (standard)" |
| 2 | [`02-template-upload.png`](../screenshots/02-template-upload.png) | The `nextwork-guardduty-setup.yaml` template uploaded |
| 3 | [`03-stack-name-details.png`](../screenshots/03-stack-name-details.png) | Stack named, template details confirmed |
| 4 | [`04-stack-failure-options.png`](../screenshots/04-stack-failure-options.png) | Stack failure options configured (rollback behavior) |
| 5 | [`05-stack-review-iam-ack.png`](../screenshots/05-stack-review-iam-ack.png) | Review page, IAM resource acknowledgement checked |
| 6 | [`06-osaka-region-mapping-error.png`](../screenshots/06-osaka-region-mapping-error.png) | **Troubleshooting:** Osaka deployment failing at template validation with the CloudFront prefix list mapping error, before any resources were created |
| 7 | [`07-stack-rollback-failed-error.png`](../screenshots/07-stack-rollback-failed-error.png) | **Troubleshooting:** Oregon deployment stuck in `ROLLBACK_FAILED` after the Auto Scaling Group signal timeout |
| 8 | [`08-stack-delete-retain-bucket.png`](../screenshots/08-stack-delete-retain-bucket.png) | **Troubleshooting:** deleting the failed stack with `TheSecureBucket` set to Retain |
| 9 | [`09-stack-create-complete.png`](../screenshots/09-stack-create-complete.png) | Final successful deployment in Oregon, status `CREATE_COMPLETE` |
| 10 | [`10-outputs-tab.png`](../screenshots/10-outputs-tab.png) | Outputs tab showing `JuiceShopURL`, `TheSecureBucket`, and other output values used throughout the attack |

## Accessing the app and the attack

| # | Screenshot | What it proves |
|---|------------|----------------|
| 11 | [`11-juiceshop-landing-page.png`](../screenshots/11-juiceshop-landing-page.png) | Juice Shop reachable at the CloudFront URL |
| 12 | [`12-sql-injection-login-field.png`](../screenshots/12-sql-injection-login-field.png) | `' or 1=1;--` entered in the login email field, before submitting |
| 13 | [`13-sql-injection-success-banner.png`](../screenshots/13-sql-injection-success-banner.png) | "Successfully solved a challenge: Login Admin" banner — authentication bypass confirmed |
| 14 | [`14-admin-profile-page.png`](../screenshots/14-admin-profile-page.png) | Admin profile page reached after the bypass |
| 15 | [`15-command-injection-object-object.png`](../screenshots/15-command-injection-object-object.png) | Username now reads `[object Object]`, confirming the payload executed |
| 16 | [`16-stolen-credentials-json.png`](../screenshots/16-stolen-credentials-json.png) | Browser showing the exposed `credentials.json` contents (temporary credentials, safe since expired and deleted) |

## CloudShell exfiltration

| # | Screenshot | What it proves |
|---|------------|----------------|
| 17 | [`17-cloudshell-first-open.png`](../screenshots/17-cloudshell-first-open.png) | CloudShell terminal opened and ready |
| 18 | [`18-cloudshell-env-vars.png`](../screenshots/18-cloudshell-env-vars.png) | `JUICESHOPURL` and `JUICESHOPS3BUCKET` environment variables set |
| 19 | [`19-cloudshell-wget-credentials.png`](../screenshots/19-cloudshell-wget-credentials.png) | `wget` downloading `credentials.json` into CloudShell |
| 20 | [`20-cloudshell-cat-jq-credentials.png`](../screenshots/20-cloudshell-cat-jq-credentials.png) | `cat credentials.json \| jq` output, formatted credentials |
| 21 | [`21-cloudshell-aws-configure-profile.png`](../screenshots/21-cloudshell-aws-configure-profile.png) | `aws configure set profile.stolen...` commands building the CLI profile |
| 22 | [`22-cloudshell-s3-cp-secret-file.png`](../screenshots/22-cloudshell-s3-cp-secret-file.png) | `aws s3 cp ... --profile stolen` copying the secret file using stolen credentials |
| 23 | [`23-cloudshell-secret-file-contents.png`](../screenshots/23-cloudshell-secret-file-contents.png) | `cat secret-information.txt` showing the leaked message — data theft confirmed |

## GuardDuty detection

| # | Screenshot | What it proves |
|---|------------|----------------|
| 24 | [`24-guardduty-findings-list.png`](../screenshots/24-guardduty-findings-list.png) | GuardDuty findings list, showing the High-severity finding |
| 25 | [`25-guardduty-finding-detail.png`](../screenshots/25-guardduty-finding-detail.png) | Finding detail page: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`, resource, action, and actor fields |

## Malware Protection extension

| # | Screenshot | What it proves |
|---|------------|----------------|
| 26 | [`26-malware-protection-enabled.png`](../screenshots/26-malware-protection-enabled.png) | "Successfully enabled Malware Protection for S3" banner |
| 27 | [`27-eicar-file-upload.png`](../screenshots/27-eicar-file-upload.png) | EICAR test file uploaded to the bucket |
| 28 | [`28-eicar-finding.png`](../screenshots/28-eicar-finding.png) | Resulting `Object:S3/MaliciousFile` GuardDuty finding |

## Cleanup

| # | Screenshot | What it proves |
|---|------------|----------------|
| 29 | [`29-cleanup-stack-delete.png`](../screenshots/29-cleanup-stack-delete.png) | CloudFormation stack deletion in progress/complete |
| 30 | [`30-cleanup-s3-template-bucket.png`](../screenshots/30-cleanup-s3-template-bucket.png) | Auto-created `cf-templates-...` bucket emptied and deleted |
| 31 | [`31-cleanup-cloudshell-files-removed.png`](../screenshots/31-cleanup-cloudshell-files-removed.png) | `ls` in CloudShell showing no files left (`credentials.json` and `secret-information.txt` removed) |
