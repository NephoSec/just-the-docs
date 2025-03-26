---
layout: page
title: SES Setup
parent: AWS Setup
---

## Verify Email Identity

1. After successful CFT Deployment, you will need to verify the email identity through AWS. The email address used 
would have received a verification email that looks like this: <br><br>
![image](../../images/WCEI-VerifyEmailfromAWS.png)<br><br>
2. Click the link and verify the email identity.
3. You will get a success response from Amazon that looks like this:<br><br>
![image](../../images/WCEI-VerifyEmailSuccess.png)<br><br>


## Add Authorization Policy to SES Identity

After the CFT successfully deploys, you will need to add a policy to the SES Identity being used for this deployment. Using the 
policy below, replace the variables in the Principal value and also replace the variables in the resource ARN with the 
newly created resource data from the CFT Output. You will need to replace `<account-id>`, `<region>` and `<identity>`.<br>
1. Navigate to the Simple Email Service page and then select the Domain Identity or Email Identity used during installation.
2. From here, select the "Authorization" tab then open the "Create policy" drop down menu and choose "Create custom policy":<br><br>
![image](../../images/WCEI-AddSESIdentityPolicy.png)<br><br>
3. Paste your filled policy template from below and save. If this step is done incorrectly you will see permission denied
errors in the log when the lambda tries to send mail using the target SES identity. 

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AllowLambdaRoleSendRawMail",
            "Effect": "Allow",
            "Principal": {
                "AWS": "arn:aws:sts::<account-id>:assumed-role/NephoSecCustomEmailIntegrationLambdaRole/NephoSecCustomEmailIntegrationLambda"
            },
            "Action": [
                "ses:SendRawEmail",
                "ses:GetEmailIdentity",
                "ses:GetEmailIdentityPolicies"
            ],
            "Resource": "arn:aws:ses:<region>:<account-id>:identity/<identity>",
            "Condition": {}
        }
    ]
}
```