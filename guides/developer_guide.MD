# Developer Guide

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