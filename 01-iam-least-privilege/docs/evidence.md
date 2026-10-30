# Evidence Index

What each screenshot in [`../screenshots/`](../screenshots) demonstrates.

## Step 1: EC2 instances

| Screenshot | What it shows |
|------------|---------------|
| [`06-prod-instance-env-tag.png`](../screenshots/06-prod-instance-env-tag.png) | Production instance named `nextwork-prod-hugh` with tag `Env=production` |
| [`07-ami-selection.png`](../screenshots/07-ami-selection.png) | AMI selected for the instances |
| [`08-instance-type-selection.png`](../screenshots/08-instance-type-selection.png) | Low-cost instance type selected |
| [`09-prod-instance-launch-success.png`](../screenshots/09-prod-instance-launch-success.png) | Production instance launched successfully |
| [`10-dev-instance-env-tag.png`](../screenshots/10-dev-instance-env-tag.png) | Development instance named `nextwork-dev-hugh` with tag `Env=development` |
| [`11-ami-selection.png`](../screenshots/11-ami-selection.png) | Low-cost instance type selected |
| [`12-instance-type-selection.png`](../screenshots/12-instance-type-selection.png) | Low-cost instance type selected |
| [`13-dev-instance-launch-success.png`](../screenshots/13-dev-instance-launch-success.png) | Production instance launched successfully |
| [`14-instances-list-running.png`](../screenshots/14-instances-list-running.png) | Both instances running |
| [`15-instance-tags-1.png`](../screenshots/15-instance-tags-1.png) | Tags tab confirming `Env=production` on the instance |
| [`16-instance-tags-2.png`](../screenshots/16-instance-tags-2.png) | Tags tab confirming `Env=production` on the instance |
| [`17-instance-tags-3.png`](../screenshots/17-instance-tags-3.png) | Tags tab confirming `Env=development` on the instance |
| [`18-instance-tags-4.png`](../screenshots/18-instance-tags-4.png) | Tags tab confirming `Env=development` on the instance |

## Step 2: IAM policy

| Screenshot | What it shows |
|------------|---------------|
| [`19-policy-json.png`](../screenshots/19-policy-json.png) | The tag-based JSON policy in the policy editor |
| [`20-policy-name-description.png`](../screenshots/20-policy-name-description.png) | `NextWorkDevEnvironmentPolicy` name and description (apostrophe removed) |
| [`21-policy-creation-success.png`](../screenshots/21-policy-creation-success.png) | IAM policy `NextWorkDevEnvironmentPolicy` created successfully |
| [`22-policy-name-listed.png`](../screenshots/22-policy-name-listed.png) | IAM policy `NextWorkDevEnvironmentPolicy` listed under policies |
| [`23-policy-details.png`](../screenshots/23-policy-details.png) | IAM policy `NextWorkDevEnvironmentPolicy` details |

## Step 3: Account alias

| Screenshot | What it shows |
|------------|---------------|
| [`24-account-alias-popup.png`](../screenshots/24-account-alias-popup.png) | Account alias created for the sign-in URL |
| [`25-account-alias-creation-success.png`](../screenshots/25-account-alias-creation-success.png) | Account alias created successfully |

## Step 4: Group and user

| Screenshot | What it shows |
|------------|---------------|
| [`26-user-group-create.png`](../screenshots/26-user-group-create.png) | `nextwork-dev-group` with the policy attached |
| [`27-user-group-success.png`](../screenshots/27-user-group-success.png) | Group created successfully |
| [`28-user-group-summary.png`](../screenshots/28-user-group-summary.png) | Created group summary |
| [`29-iam-user-create.png`](../screenshots/29-iam-user-create.png) | User `nextwork-dev-Kumbi` created with console access |
| [`30-user-add-to-group.png`](../screenshots/30-user-add-to-group.png) | User added to the group (permissions inherited, none attached directly) |
| [`31-user-details-review.png`](../screenshots/31-user-details-review.png) | User details summary prior to creation |
| [`32-user-signin-details.png`](../screenshots/32-user-signin-details.png) | Sign-in details (redacted, not committed if it shows credentials) |

## Step 5: Access tests as the intern

| Screenshot | What it proves |
|------------|----------------|
| [`33-signin-as-iam-user.png`](../screenshots/33-signin-as-iam-user.png) | Signed in as the IAM user through the alias URL |
| [`34-access-denied-dashboard.png`](../screenshots/34-access-denied-dashboard.png) | Services outside the policy are denied by default |
| [`35-stop-prod-denied-error.png`](../screenshots/35-stop-prod-denied-error.png) | **Production protected:** stopping the prod instance fails when not authorized |
| [`36-ec2-dashboard-resources.png`](../screenshots/36-ec2-dashboard-resources.png) | **EC2 resources protected:** access fails without authorized |
| [`37-stop-dev-success.png`](../screenshots/36-stop-dev-success.png) | **Development allowed:** stopping the dev instance succeeds |

## Secret mission: IAM Policy Simulator

| Screenshot | What it proves |
|------------|----------------|
| [`38-simulator-deny-statement.png`](../screenshots/38-simulator-deny-statement.png) | Which policy statement blocks `DeleteTags` |
| [`39-simulator-development-tag.png`](../screenshots/39-simulator-development-tag.png) | Resource scoped to the `development` tag |
| [`40-simulator-allow-statement.png`](../screenshots/40-simulator-allow-statement.png) | Which policy statement allows `StopInstances` |
| [`41-simulator-results.png`](../screenshots/41-simulator-results.png) | Final results: `DeleteTags` denied, `StopInstances` allowed for tagged resources |

## Cleanup

| Screenshot | What it shows |
|------------|---------------|
| [`42-cleanup-instances.png`](../screenshots/42-cleanup-instances.png) | Instances, group, user and policy removed |
| [`43-cleanup-group.png`](../screenshots/43-cleanup-group.png) | Group removed |
| [`44-cleanup-user.png`](../screenshots/44-cleanup-user.png) | User removed |
| [`45-cleanup-policy.png`](../screenshots/45-cleanup-policy.png) | Policy removed |
