# Deployment guide

**NOTE:**

This document contains partial information, it needs to be augmented with data from all squads that worked on the Kesher project

## About this project
This project uses CDK to deploy its resources to AWS. 
CDK is *"a software development framework for defining cloud infrastructure in code and provisioning it through AWS CloudFormation."*
To get started with CDK see [the docs](https://docs.aws.amazon.com/cdk/latest/guide/home.html)

## Prerequisites 
* Before you start you will need to register a domain name with AWS route 53. head over to the domain registration guide for details

* TODO: Describe the steps needed to take before deployment, especially installation of all tools (pipenv, node, cdk, docker, etc.)

**A NOTE ON OS COMPATIBILITY:**
This was developed and tested on Mac OS, There might be compatibility issues when running on another OS (Windows), especially in OS specific areas like file system access.

## Before deploying 
* Due to SES (AWS Simple Email Service) requirements, you must deploy your project to either eu-west-1, us-east-1 or us-west-2
* To be able to email using SES, you must either verify the email address by sending them a link - see the email verification steps below

## Deployment

To deploy this application, you first need to deploy the global stack, by running:

`cdk deploy --app cdk_global/app.py`

This will deploy MX records for your domain, which will allow us to send and receive emails using this domain name. Since these are DNS records, they may take some time to propagate.

Next, deploy the app by running:

`./deploy.py`

## Post Deploy Steps

### Activating the domain in SES
To be able to send and receive emails with the domain name you have purchased, you need to activate it with SES. 
1. After logging in to the AWS console, Open the Route 53 service, and copy your domain name that you previously registered.
1. Open SES, click on "Domains", "Verify a New Domain"
1. Enter your domain name, click verify
1. On the next page, click "User Route 53"
1. Make sure "Email Receiving Record" is checked (everything should be checked here)
1. Click on verify
1. This can take up to a few minutes to complete 

### Email Verification Steps

You need to run this procedure when creating the admin user. 
This is required so that we can email this address. 
Alternatively an account can be ["Moved out of SES sandbox"](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html) and email unverified addresses.

1. Log in to the AWS console
1. Open the SES service page (click the "services" drop-down and search for SES (Simple Email Service)
1. Click on "Email Addresses" and then "Verify a New Email Address"
1. Enter the admin email and click "Verify Email Address"
1. You can review the verification status. make sure the admin click the verification link and then the status should change to "Verified"
1. If you need to, after you did this, you can resend the link from the same page

