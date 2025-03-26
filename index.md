---
layout: default
title: Home
nav_order: 1
description: "NephōSec Wiz Email Integration documentation hosted on GitLab Pages."
permalink: /
---

# NephōSec Wiz Email Integration Documentation
{: .fs-9 }

Easily send branded emails from Wiz generated issues 
{: .fs-6 .fw-300 }

[Download CloudFormation Template](https://wiz-custom-email-integration-cft-bucket.s3.us-east-1.amazonaws.com/wiz-custom-email-integration-cft%3Alatest.yaml){: .btn .fs-5 .mb-4 .mb-md-0 target="_blank"} [CloudFormation Quick Launch](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/stacks/create/review?templateURL=https://wiz-custom-email-integration-cft-bucket.s3.us-east-1.amazonaws.com/wiz-custom-email-integration-cft%3Alatest.yaml&stackName=NephoSecCustomWizEmailIntegration){: .btn .fs-5 .mb-4 .mb-md-0 target="_blank"}

You must be Authenticated to AWS to use the links above.

---

## Contents

- [NephōSec Wiz Email Integration Documentation](#nephōsec-wiz-email-integration-documentation)
  - [About](#about)
  - [Prerequisites](#prerequisites)
  - [AWS Infrastructure](#aws-infrastructure)
  - [Updating Wiz Email Integration Module](#updating-wiz-email-integration-module)
- [Templates](#templates)
- [Custom Templates](#custom-templates)
  - [Logs](#logs)
  - [Common Issues](#common-issues)
  - [Support](#support)

---

## About

You can configure Wiz to send pre-formatted emails to selected recipients, either by manually running an Action on one 
or more Issues or automatically using an Automation Rule. The NephoSec Wiz Email Integration allows you to utilize 3 
default email templates, as well as the ability to customize and add your own template(s). 

---

## Prerequisites

Before you get started, make sure you have the following:

* An active Wiz License
* At least one active Wiz Deployment
* Access to Wiz User granted with a Wiz Role that has Integrations create or write permissions, Automation Rules create or write permissions
* Access to Wiz User granted with a Wiz Role that has Automation Rules create or write permissions
* Access to Wiz User granted with a Wiz Role that has Automation Actions create or write permissions
* Access to AWS User/Service Account with Role/Policy attached that grants creation of CloudFormation Stack
* AWS Simple Email Service environment that has been upgraded from sandbox mode. This is done on a per-account basis. 
**[AWS Documentation](https://docs.aws.amazon.com/ses/latest/dg/request-production-access.html)**<br>
* A license key from NephoSec **this will be manually emailed to you after signing up for the product on the [NephoSec site](https://nephosec.com/wiz-email-integration-module/)**<br>

---

## AWS Infrastructure

Below are the resources created by the CloudFormation Template:

- Lambda Function: Function, Role, Policy, Log Group, EventSourceMapping
- SimpleEmailService (SES) Identity: Sender Email
- SimpleQueueService (SQS) Queue: Queue, Policy, DeadLetter Queue
- SimpleNotificationService (SNS) Subscription: Subscription, Topic


### Updating Wiz Email Integration Module
New release of the Wiz Email Integration Module are released as container images. The lambda is by default pointed at 
the latest tag on the container registry. Even so new versions of the image are not used until the lambda is explicitly 
told to pull a new image. You can do so by following the instructions below. Release notes will be listed here on the 
documentation site. 

1. Navigate to Lambda Functions
2. Locate and select "NephoSec Wiz Integration Module"
3. Under "Image" select "Deploy New Image"
4. Click "Save" and wait for new image deployment

---


# Templates

Three templates are provided for varying degrees of severity/user response. These can be used by referencing the below 
template names in the action template. The "template" key can be found under the "resource" key.

* For_Your_Action
* For_Your_Awareness
* For_Your_Information

# Custom Templates

If your organization is interested in customizing an email template to use with this integration please reach out to us 
for design requirements. 

___

## Logs

To troubleshoot or capture issues/errors a Log Group named `/aws/lambda/NephoSecCustomEmailIntegrationLambda` is created
during the CloudFormation deployment. To access the Log Group start at the AWS Console and navigate to the "CloudWatch" 
service page. Once there, look for "Logs" on the left pane and select "Log Groups".  The Log Group can also be found in 
the "Monitor" tab of the Lambda Function page. Located at the top right of this view is a "View Cloudwatch Logs" button."

___


## Support

For assistance or feature requests related to NephoSec's Wiz Email Integration Module, please join our community [Slack](https://nephosec-community.slack.com/ssb/redirect#/shared-invite/email){: target="_blank"} and post in the `#wiz-email-integration-module` channel.
