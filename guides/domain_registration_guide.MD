# Manual Domain Registration

To be able to use SES to receive email messages from users, we need to register a domain name in route 53 (It is possible to use your own domain but the process is more complex. 
The registration process is described here: 
https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/domain-register.html 

And below you can find the steps we took to create the dev domain. 
It is highly recommended that you read the aws documentation to get all the details BEFORE you go through with the registration.

## A few notes: 
* This costs 12$/yr
* After you register the domain name, you cannot change it.

## Registration steps:
1. open the AWS console, go to the Route 53 page.
1. click on "Register Domain" (You might need to open the side bar, depends on your view)
1. select the top level domain name, we used .com which is one of the cheapest (12$/yr)
1. check for availability and update as needed, when ready, add to cart and continue.
1. We selected "Yes" for "My Registrant, Administrative and Technical Contacts are all the same"
1. Fill in the details as needed. to avoid spammers you need to use actual info and a working email which will be used for verification.
   1. We use "Person" for "Contact Type"
   1. A work-place address will probably be the best
   1. Enable "Privacy Protection"  
1. Click Continue
1. You should be getting the verification email and click on the verify link
1. Back in the aws console you can click on "refresh status" and see that it changed to verfied. 
1. Make sure "Do you want to automatically renew your domain?" is enabled
1. Agree to the term and conditions and click "Complete order"
1. You should get a success message telling you that "If any additional payment-related steps are required, your billing contact will receive further instructions in an email.", but generally you are done.
1. Click on "Go to domains" (Note that your domain is under "Pending Domains" until registration completes) where you will see your newly purchased domain with a "Domain registration in progress" 
1. The registration process can take anything from a few minutes to a few hours in all is well. my registration took 15 minutes
1. In the kesher-backend project, make sure that the domain name is specified correctly. update the values of KESHER_DOMAIN_NAME as needed in: cdk/kesher_service_cdk/service_stack/constants.py

## Troubleshooting
* If your domain registration is stuck at "Domain registration in progress: To finish registering your domain, you must verify your payment" see this: https://forums.aws.amazon.com/thread.jspa?threadID=261920
* 

