# S3 Bucket Access

### Requirements

* Secure access and secret keys storage
* Separate policies for read and write
* Credential rotation
* Additional access restrictions based on DC IP or DX VIF IP
* Monitoring

### Scenarios
#### Scenario 1 
Bucket level policies for separate read and write users.

#### Scenario 2
* Separate IAM roles for read and write
* Allow approved users to assume required role

#### Scenario 3
* Access limited to RH account

### Setup
* Create IAM users
* Create bucket
* Create IAM roles and bucket policy