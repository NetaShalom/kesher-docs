# Developer Guide

## Data upload flows
Due to lack of time and resources, we couldn't create designated frontend for the admin and teacher for uploading data into the system. 
Because of this, we came up with a simpler way to perform these operations. 
These are the data upload to the system by the admin (list of kindergartens) and the teachers (list of children in each kindergarten).

### Data upload by admin.
1. An email is sent from adminsubmit@{KESHER_DOMAIN_NAME} (whatever you select your domain name to be) with an attachment of the kindergartens templates for admin to fill. 

1. Admin receives this email, fill the csv template and replies to the sender. 

**(GAP): The email create and sending needs to be implemeted**

* Dev notes:
  * See EmailServices (cdk/kesher_service_cdk/service_stack/email_services/email_services.py) - it deploys the email services and the lambda functions that handles these flows.
  * All email lambdas are here: cdk/kesher_service_cdk/service_stack/email_services/functions/handler.py

Under the hood this goes as follow:

1. Something (probably a Lambda), create the email - generate a pre-signed url for uploading. this is a special url, which is signed cryptographically, has an expiration (the amount of time you want to let the user do this - up to a week), and it points to an S3 object. 
see "create_presigned_post" function for generating a presigned url.

1. we attach the template (or a populated file if the db is not empty) and respond with an email to admin with the template file and the presigned url.

1. When the user replies back with th populated file and the presigned url, we can verify that they actually have the presigned url. its a form of authorization, so that not anyone can email us with data. 

1. the "admin_submit" lambda receives the user email response, and reads the email content from an S3 bucket (when SES receives this email it stores it in an S3 bucket and trigger an Lambda).
We then try to upload the template file with the presigned URL, thus verifying the user. 
We then proceed to insert the data into the database (beware of SQL injection!) and respond to the user with the result (error/success)

**(GAPS):** 
* Create a small infrastructure for inserting data into the database securely (protect against SQL injections), perhaps with SQL prepared statements.
* Implement the admin_submit logic (The code has some TODOs that needs to be implemented)
* On each successful population of a teacher email, generate an email for them with the children list template. if this is a first time, then we need to generate an email verification for the user and they MUST click on the verify link before then can get actual emails. 
* If this is the first time, how do we email them again?

### Data upload by teachers.
This flow is very similar to the admin submit, but here we 
only get the email template from the reacher and read the response.
See: 
teacher_submit in cdk/kesher_service_cdk/service_stack/email_services/functions/handler.py


## Kesher Email Services

We are using SES to deliver and receive email messages to users. 
using SES this way requires ownership of a registered domain name which is deployed in the global stack. see [Kesher Global Stack wiki](https://github.com/CyberArkForTheCommunity/kesher-backend/wiki/Kesher-Global-Stack) for more info.

A few notes about SES:

1. To avoid spamming and abuse, all accounts are defaulted to a "sandbox" mode. this means that you can:
* Send a maximum of 200 messages per 24-hour period.
* Only email verified addresses.
* Only email FROM verified addresses (should be ok since we're using our route 53 domain).
* You can send a maximum of 1 message per second (We need to make sure its ok in our lambdas).

If we want to get out of the sandbox, we need to go through some procedure which is described here:
https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html

we will try to automate this, but i'm guessing it will defeat the anti-spam intended, so may be a little tricky.
the worst case is that users will need to verify their email addresses.

2. We are required to register (purchase) a domain name. 

3. If you want to eliminate the requirement of email verification, you can ["Move out of the sandbox"](https://docs.aws.amazon.com/ses/latest/DeveloperGuide/request-production-access.html), but this is a procedure which meaning is that you move your account into production

4. Since at least in the first version, we accept user input via email, which is not authenticated, we are using something called presigned url for authorization. this means that we generate a signed url in the backend and email it to the user. this signed url acts as a secret that the user then responds with to us and we can verify that this user sender is indeed one that we provided to it. 