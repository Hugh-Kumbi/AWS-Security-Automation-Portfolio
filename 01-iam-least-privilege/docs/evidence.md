# Evidence Index

What each screenshot in [`../screenshots/`](../screenshots) demonstrates.

## Step 1: EC2 instances

| Screenshot | What it shows |
|------------|---------------|
| [`06-prod-instance-env-tag.png`](../screenshots/06-prod-instance-env-tag.png) | Production instance named `nextwork-prod-hugh` with tag `Env=production` |
| [`07-ami-free-tier.png`](../screenshots/07-ami-free-tier.png) | Free-tier AMI selected |
| [`08-instance-type-free-tier.png`](../screenshots/08-instance-type-free-tier.png) | Free-tier instance type selected |
| [`11-prod-instance-launch-success.png`](../screenshots/11-prod-instance-launch-success.png) | Production instance launched successfully |
| [`12-instances-list-running.png`](../screenshots/12-instances-list-running.png) | Both instances running |
| [`13-instance-tags-1.png`](../screenshots/13-instance-tags-1.png) to [`16-instance-tags-4.png`](../screenshots/16-instance-tags-4.png) | Tags tab confirming `Env=production` and `Env=development` on each instance |

## Step 2: IAM policy

| Screenshot | What it shows |
|------------|---------------|
| [`19-policy-json-pasted.png`](../screenshots/19-policy-json-pasted.png) | The tag-based JSON policy in the policy editor |
| [`20-policy-name-description.png`](../screenshots/20-policy-name-description.png) | `NextWorkDevEnvironmentPolicy` name and description (apostrophe removed) |

## Step 3: Account alias

| Screenshot | What it shows |
|------------|---------------|
| [`23-account-alias-popup.png`](../screenshots/23-account-alias-popup.png) | Account alias created for the sign-in URL |

## Step 4: Group and user

| Screenshot | What it shows |
|------------|---------------|
| [`24-user-group-create.png`](../screenshots/24-user-group-create.png) | `nextwork-dev-group` with the policy attached |
| [`25-user-group-success.png`](../screenshots/25-user-group-success.png) | Group created successfully |
| [`26-iam-user-create.png`](../screenshots/26-iam-user-create.png) | User `nextwork-dev-Kumbi` created with console access |
| [`28-user-add-to-group.png`](../screenshots/28-user-add-to-group.png) | User added to the group (permissions inherited, none attached directly) |
| `29-user-signin-details.png` | Sign-in details (redacted, not committed if it shows credentials) |

## Step 5: Access tests as the intern

| Screenshot | What it proves |
|------------|----------------|
| [`30-signin-as-iam-user.png`](../screenshots/30-signin-as-iam-user.png) | Signed in as the IAM user through the alias URL |
| [`31-access-denied-dashboard.png`](../screenshots/31-access-denied-dashboard.png) | Services outside the policy are denied by default |
| [`34-stop-prod-denied-error.png`](../screenshots/34-stop-prod-denied-error.png) | **Production protected:** stopping the prod instance fails with not authorized |
| [`35-stop-dev-success.png`](../screenshots/35-stop-dev-success.png) | **Development allowed:** stopping the dev instance succeeds |

## Secret mission: IAM Policy Simulator

| Screenshot | What it proves |
|------------|----------------|
| [`38-simulator-deny-statement.png`](../screenshots/38-simulator-deny-statement.png) | Which policy statement blocks `DeleteTags` |
| [`39-simulator-development-tag.png`](../screenshots/39-simulator-development-tag.png) | Resource scoped to the `development` tag |
| [`40-simulator-results.png`](../screenshots/40-simulator-results.png) | Final results: `DeleteTags` denied, `StopInstances` allowed for tagged resources |

## Cleanup

| Screenshot | What it shows |
|------------|---------------|
| [`41-cleanup-instances.png`](../screenshots/41-cleanup-instances.png) to [`44-cleanup-policy.png`](../screenshots/44-cleanup-policy.png) | Instances, group, user and policy removed |
