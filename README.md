# S3 Bucket Access

### Requirements

* Secure access and secret keys storage
* Separate policies for read and write
* Credential rotation
* Encryption at rest & in transit
* Additional access restrictions based on Heroku IP or underlying AWS account
* Logging & monitoring

### Scenarios
#### Scenario 1 
Bucket level policies for separate read and write users.

#### Scenario 2
* Separate managed policies for read and write
* Separate read and write users with respective managed policies assigned to them.

#### Scenario 3
* Scenario #2 w/ access limited to RH via account-to-account access

### Setup
* Create IAM users
* Create bucket
* Create IAM policy and roles