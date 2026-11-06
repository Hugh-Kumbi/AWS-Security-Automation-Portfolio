# Evidence Index

What each screenshot in [`../screenshots/`](../screenshots) proves. Filenames below are what the README and incident report link to — rename your files to match, or do a find-and-replace across all three files if you'd rather keep your own names.

If you don't have a screenshot for a row, delete the row (and remove the matching placeholder from the README/incident report) rather than leaving a broken image link.

## CloudFormation deployment

| # | Screenshot | What it proves |
|---|---|---|
| 1 | `01-stack-create-start.png` | Starting a new stack with "With new resources (standard)" |
| 2 | `02-template-upload.png` | The `nextwork-guardduty-setup.yaml` template uploaded |
| 3 | `03-stack-name-details.png` | Stack named, template details confirmed |
| 4 | `04-stack-failure-options.png` | Stack failure options configured (rollback behavior) |
| 5 | `05-stack-review-iam-ack.png` | Review page, IAM resource acknowledgement checked |
| 6 | `06-stack-rollback-complete-error.png` | **Troubleshooting:** first deployment in `ROLLBACK_COMPLETE`, proving the region-mapping and stuck-bucket issues really happened |
| 7 | `07-asg-activity-tab-error.png` | **Troubleshooting:** Auto Scaling Group Activity tab showing the real signal-timeout error, found after disabling rollback |
| 8 | `08-stack-delete-retain-bucket.png` | **Troubleshooting:** deleting the failed stack with `TheSecureBucket` set to Retain |
| 9 | `09-stack-create-complete.png` | Final successful deployment, status `CREATE_COMPLETE` |
| 10 | `10-outputs-tab.png` | Outputs tab showing `JuiceShopURL`, `TheSecureBucket`, and other output values used throughout the attack |

## Accessing the app and the attack

| # | Screenshot | What it proves |
|---|---|---|
| 11 | `11-juiceshop-landing-page.png` | Juice Shop reachable at the CloudFront URL |
| 12 | `12-sql-injection-login-field.png` | `' or 1=1;--` entered in the login email field, before submitting |
| 13 | `13-sql-injection-success-banner.png` | "Successfully solved a challenge: Login Admin" banner — authentication bypass confirmed |
| 14 | `14-admin-profile-page.png` | Admin profile page reached after the bypass |
| 15 | `15-command-injection-payload.png` | JavaScript payload pasted into the Username field |
| 16 | `16-command-injection-object-object.png` | Username now reads `[object Object]`, confirming the payload executed |
| 17 | `17-stolen-credentials-json.png` | Browser showing the exposed `credentials.json` contents (temporary credentials, safe since expired and deleted) |

## CloudShell exfiltration

| # | Screenshot | What it proves |
|---|---|---|
| 18 | `18-cloudshell-first-open.png` | CloudShell terminal opened and ready |
| 19 | `19-cloudshell-env-vars.png` | `JUICESHOPURL` and `JUICESHOPS3BUCKET` environment variables set |
| 20 | `20-cloudshell-wget-credentials.png` | `wget` downloading `credentials.json` into CloudShell |
| 21 | `21-cloudshell-cat-jq-credentials.png` | `cat credentials.json \| jq` output, formatted credentials |
| 22 | `22-cloudshell-aws-configure-profile.png` | `aws configure set profile.stolen...` commands building the CLI profile |
| 23 | `23-cloudshell-s3-cp-secret-file.png` | `aws s3 cp ... --profile stolen` copying the secret file using stolen credentials |
| 24 | `24-cloudshell-secret-file-contents.png` | `cat secret-information.txt` showing the leaked message — data theft confirmed |

## GuardDuty detection

| # | Screenshot | What it proves |
|---|---|---|
| 25 | `25-guardduty-findings-list.png` | GuardDuty findings list, showing the High-severity finding |
| 26 | `26-guardduty-finding-detail.png` | Finding detail page: `UnauthorizedAccess:IAMUser/InstanceCredentialExfiltration.InsideAWS`, resource, action, and actor fields |

## Malware Protection extension

| # | Screenshot | What it proves |
|---|---|---|
| 27 | `27-malware-protection-enabled.png` | "Successfully enabled Malware Protection for S3" banner |
| 28 | `28-eicar-file-upload.png` | EICAR test file uploaded to the bucket |
| 29 | `29-eicar-finding.png` | Resulting `Object:S3/MaliciousFile` GuardDuty finding |

## Cleanup

| # | Screenshot | What it proves |
|---|---|---|
| 30 | `30-cleanup-stack-delete.png` | CloudFormation stack deletion in progress/complete |
| 31 | `31-cleanup-s3-template-bucket.png` | Auto-created `cf-templates-...` bucket emptied and deleted |
| 32 | `32-cleanup-cloudshell-files-removed.png` | `ls` in CloudShell showing no files left (`credentials.json` and `secret-information.txt` removed) |