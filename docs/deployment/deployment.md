---
layout: default
title: Deployment Guide
---

# Deployment Guide
This page will guide your through the required steps to deploy NephoSec's Wiz Email Integration Module. By the end of 
this guide you will be able to generate emails alerts for Wiz issues using the provided AWS SES templates.

[Download CloudFormation Template](https://wiz-custom-email-integration-cft-bucket.s3.us-east-1.amazonaws.com/wiz-custom-email-integration-cft%3Alatest.yaml){: .btn .fs-5 .mb-4 .mb-md-0 target="_blank"} [CloudFormation Quick Launch](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://wiz-custom-email-integration-cft-bucket.s3.us-east-1.amazonaws.com/wiz-custom-email-integration-cft%3Alatest.yaml&stackName=NephoSecCustomWizEmailIntegration){: .btn .fs-5 .mb-4 .mb-md-0 target="_blank"}

You must be Authenticated to AWS to use the links above.

---

## Contents

- [CloudFormation Deployment](#cloudformation-deployment)
- [Post CFT Deployment](#post-cft-deployment)
  - [Verify Email Identity](#verify-email-identity)
  - [Add Authorization Policy to SES Identity](#add-authorization-policy-to-ses-identity)
  - [Add License ID to Lambda](#add-license-id-to-lambda)
- [Wiz Setup](#wiz-setup)
  - [Wiz Integration](#wiz-integration)
  - [Wiz Automation Rule](#wiz-automation-rule)
  - [Example Action Template](#example-action-template)


---
## CloudFormation Deployment

Currently, us-east-1(N.Virginia) and us-east-2(Ohio) are the only regions supported for this deployment.<br>

If using the QuickLaunch button at the top, please ensure you are signed in to the AWS account/region you're 
deploying the CloudFormation Template (CFT). After clicking the QuickLaunch button, you are able to skip to step 5 below. 
<br><br>
If not using the QuickLaunch Button, follow the steps below:

1. Download the CloudFormation Template (CFT) from the link above
2. From the AWS Console go to the CloudFormation service page
3. Select “Create Stack” and chose “With New Resources”
4. Next, select “Choose an Existing Template” and upload the provided CFT
5. Follow the CFT deployment instructions, providing parameters where necessary (SES Identity/Address, etc) <br>
   * There are two kinds of the SES identities that can be created, an email identity and a domain identity. An email identity 
   configuration allows the sending of email from only that email address. The verification process involves opening an email
   that will be sent to the inbox of the address provided to prove you own the account. Alternatively there is the domain 
   identity which allows sending of email from any address in that domain. In order to verify domain ownership, the provided
   cname records must be created in the domain to prove domain ownership.
   * If your organization is already using SES you can provide the identity in the `EmailOrDomainIdentity` field and select **true** for `ExistingSESIdentity`.
   * If your organization does not already use SES, provide an email address that you have access to in the `EmailOrDomainIdentity` field and select **false** for `ExistingSESIdentity`
   ![image](../../images/WCEI-CFTParameters.png)<br>
6. Follow launch wizard process within AWS until you're able to "create stack".
![image](../../images/WCEI-CFTConfirmation.png)<br><br>

---

##  Post CFT Deployment

After successfully deploying the CFT, find the Outputs tab and make note of the values for `SNSTopicArn` and 
`CustomerSESIdentityARN`. You will need both of those values for the next steps:<br><br>
![image](../../images/WCEI-CFTOutputs.png)<br>




## Add license key to Lambda

After signing up, a license key will be provided to you from NephoSec. This license key is required as an environment 
variable for the lambda function. Without a license key and lambda invocations will result in a licensing validation 
error in the cloudwatch logs. 
1. Navigate to the Lambda service in AWS. Select the NephoSecCustomEmailIntegrationLambda and click the Configuration
tab. On the left side navigation menu, select Environment Variables.<br>
![image](../../images/WCEI-LambdaSetup.png)<br><br>
2. Click Edit on the right side and add in the environment variable for `LICENSE_KEY`, click Save.<br>
![image](../../images/WCEI-LicenseKeyLambda.png)
<br>

## Wiz Setup

### Wiz Integration

1. In Wiz Console, on the left side navigation menu, select “Settings” then “Integrations” and then click `+ Add Integration`<br><br>
![image](../../images/WCEI-WizAddIntegration.png)<br><br>
2. Search and select "AWS SNS". <br><br>![image](../../images/WCEI-AddSNS.png)<br><br>
3. Fill out the name of integration and provide the SNS arn created from the previously 
deployed CloudFormation Template (`SNSTopicArn` in CFT Outputs).<br><br>
![image](../../images/WCEI-SNSArn.png)<br><br>
4. Proceed with selecting the Access Method in which you are connecting to your cloud account. For more information on 
Wiz's AWS SNS Integration, please visit [their docs](https://docs.wiz.io/wiz-docs/docs/sns-integration?lng=en). 

___

### Wiz Automation Rule

1. Select “Policies”, then under Response select “Automation Rules”<br><br>
![image](../../images/WCEI-AutomationRules.png)<br><br>
2. From the dropdown menu select “+ Add Rule” and fill out the name, description and scope fields, according to your policies/needs
3. `When` Rule Conditions - Recommended to use `Issue is Created, Updated, Reopened`.
4. `If` Filter - Select "Rule” and search for desired rule(s) for which to add automation/use email templates.
5. `Then` Actions - Select previously created integration, update the Action Template (Example below) and save template 
for future use<br><br>
![image](../../images/WCEI-AutomationRuleConfig.png)<br><br>


### Example Action Template

{% raw %}
```json
{
  "trigger": {
    "source": "{{triggerSource}}",
    "type": "{{triggerType}}",
    "ruleId": "{{ruleId}}",
    "ruleName": "{{ruleName}}",
    "updatedFields": "{{#changedFields}} {{name}} field was changed from {{previousValue}} to {{newValue}}, {{/changedFields}}",
    "changedBy": "{{changedBy}}"
  },
  "issue": {
    "id": "{{issue.id}}",
    "status": "{{issue.status}}",
    "severity": "{{issue.severity}}",
    "created": "{{issue.createdAt}}",
    "projects": "{{#issue.projects}}{{name}}, {{/issue.projects}}"
  },
  "resource": {
    "id": "{{issue.entitySnapshot.providerId}}",
    "name": "{{issue.entitySnapshot.name}}",
    "type": "{{issue.entitySnapshot.nativeType}}",
    "cloudPlatform": "{{issue.entitySnapshot.cloudPlatform}}",
    "subscriptionId": "{{issue.entitySnapshot.subscriptionExternalId}}",
    "subscriptionName": "{{issue.entitySnapshot.subscriptionName}}",
    "region": "{{issue.entitySnapshot.region}}",
    "status": "{{issue.entitySnapshot.status}}",
    "cloudProviderURL": "{{issue.entitySnapshot.cloudProviderURL}}",
    "toEmail": [
      "anthony+test@nephosec.com",
      "{{issue.entitySnapshot.tags.Owner}}"
    ],
    "template": "For_Your_Action",
    "backgroundtext": "Inserting additional background text on why this is a bad practice",
    "closingtext": "Inserting more information about where the user can obtain more information about the issue."
  },
  "control": {
    "id": "{{issue.control.id}}",
    "name": "{{issue.control.name}}",
    "description": "{{issue.control.descriptionPlainText}}",
    "severity": "{{issue.control.severity}}"
  }
}
```
{% endraw %}
