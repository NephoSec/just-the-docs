---
layout: default
title: Configuration
---

## Getting Started

- [Configuration](#configuration)
  - [Wiz Integration](#wiz-integration)
  - [Wiz Automation Rule](#wiz-automation-rule)
  - [How to update Wiz Email Integration Module](#how-to-update-wiz-email-integration-module)

---

# Configuration
{: .no_toc }


---

## Wiz Integration

1. Select “Settings” then “Integrations” and then click “+ Add Integration”
2. Choose “Webhook” and fill out the name, description and scope fields, according to your policies/needs
3. Provide the Function URL generated from the lambda created from the previously deployed CloudFormation Template
4. Proceed with "Add Integration", the rest of the configuration can be left blank/default\*

___

## Wiz Automation Rule

1. Select “Policies” then under Response select “Automation Rules”
2. From the dropdown menu select “+ Add Rule” and fill out the name, description and scope fields, according to your policies/needs
3. (When) Rule Conditions - Recommended to use Issue is Created, Updated, Reopened
4. (If) Filter - Select “Rule” and search for desired rule to add automation to
5. (Then) Actions - Select previously created integration, update the Action Template (Example below) and save template for future use

___

## How to update Wiz Email Integration Module

1. Navigate to Lambda Functions
2. Locate and select "Nephosec Wiz Integration Module"
3. Under "Image" select "Deploy New Image"
4. Ensure container is tagged with ":latest"
5. Click "Save" and wait for new image deployment
