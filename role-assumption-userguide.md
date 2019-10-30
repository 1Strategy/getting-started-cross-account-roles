# AWS Multi-Account Structure End-User Guide

## Switching roles in the AWS Console:

1. An AWS administrator will create a user for you in the Master account and email your credentials
2. Log in to the Master AWS Account with your IAM username and account alias. You will be prompted to reset your password
3. After resetting your password, log into the console
4. Find your username in the upper righthand corner, and select "My Security Credentials"
5. Next to "Assigned MFA device" it will say None. Click Manage
6. You will need to have an MFA application installed on your phone. If you already have an app, you can use that
7. You will need to enter 2 verification codes from your device to authenticate your MFA
8. Log out of the AWS console
9. Log back into the console using your IAM user in the Master account. This time you will be prompted for an MFA code
10. Click on your username in the top right of the console and select "Switch Role"
11. Enter the account alias, role name (most likely one of AWSAdminRole, DeveloperRole, ReadOnlyRole), select a color, and click Switch Role
12. By default, the display name is "RoleName @ account-alias", but you can enter a custom name here
13. Repeat steps for the Development Account account

*now you can easily switch between roles in multiple accounts, and return to the master account by selecting "back to username" from the username dropdown in the top right*

## Using Role Assumption in the AWS CLI:

1. Create a new secret access key in the Master account
2. Create a profile in .aws/credentials and place the secret access key and ID there

```
[mycompany-master]
aws_access_key_id = ACCESSKEYIDABCDEFG
aws_secret_access_key = SecretAccessKey123abc123
```

3. In the Production account, visit the IAM console and grab the Role ARN for the role you wish to use in that account
4. Create another profile in your credentials file and use this role like so:

```
[mycompany-prod]
role_arn       = arn:aws:iam::1234account#:role/ROLENAME
source_profile = mycompany-master
```

5. Repeat steps 3 and for for any remaining member accounts you wish to access via the CLI
6. Be sure to set your default profile to one you wouldn't mind using on accident, we recommend a development account
7. Now, try some of these commands to validate your CLI access (note that these are dependent upon your Role's permissions)

```aws iam list-account-aliases --profile mycompany-master```

this should return the alias of your master account

```aws sts get-caller-identity --profile mycompany-prod```

this should return the AWS account ID and STS ARN

```aws s3 ls```

notice that in this example, I excluded the --profile flag.
This will return a list of S3 buckets using credentials for your default profile
