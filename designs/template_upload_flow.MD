# Kindergarten template flow design

The kindergarten teacher needs to be able to upload a csv (excel) file with the children details for the kindergarten.
They are supposed to get this via an initial email with the csv template file which they will fill and upload.
They need to be able to create, update and in the future delete records.

Suggested solution:

## Using SES, Lambda, presigned urls

*  **Question**: How does the kindergarten teacher gets this email (what triggers this and how)?
* Bootstrapping email: 
   * Create a pre-signed url for the email - for uploading the file - this requires a bucket name, object name. the user that created the pre-signed url must have the permissions to upload the object
   * Presigned url should be limited to expire after a certain point
   * Attach the csv template file 
   * Send email 
* Kindergarten teacher reply
   * User replies with the populated csv file (and the presigned url in the email)
   * SES will be configured with rules to accept this response and act upon it
   * Since we know the user email (we emailed them), we can set a rule to process any email for them and bounce from any other sender
   * When we receive an email from the kindergarten teacher, trigger the processing lambda
* Email processing lambda
   * Check presigned url for expiration and validity. If expired reply with the meaningful message.
   * If not expired, upload file to S3 for history/backup (that's where the presigned url is used)
   * Process the file - insert/update to DB - this can be done in another lambda that will be triggered by the s3 upload (but can be done with the same lambda instead)
   * Deletion - not implemented. Should be in another process and not a file upload.
   * Since this relies on use input, we have to validate this csv with care. 
				
		
Nots:
* All senders/recipients need to verify emails when using SES
* Technically we don't have to use a presigned url, but its simple to generate, and we can later use it to upload the file it other ways.
* If we, for some reason don't want to use a presigned url, we need to create another expiring link or token to use with the email.

--------------

## Alternative:

* Do we need to send an email the same way? If so, we still need SES with a domain.
* If not, the upload can be done via some API (called from an app/web app)
* We can use presigned urls for uploading which can be received via the email initially 

	------------

## Next steps
* Support deletion - in any case we should implement the deletion flow using another mechanism and not create a mirror image of the uploaded csv, in which an accidental omission of a row will cause a child to be deleted from the kindergarten.
