# Gaps and Open Issues

* The data upload by the admin and the teachers needs to be fully implemented

* Each target email (admin and teachers) must be verified before you can start sending to them. see the deployment guide for more details.

* This was tested on Mac OS and was not tested running on windows. 

* User input safety - when write data to the database, you must verify that you eliminate the possibility for SQL injection. generally in MySQL you would use prepared statements, but this may depend on the way that its implemented.

* 