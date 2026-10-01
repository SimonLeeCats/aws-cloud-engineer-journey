What is ARN?
ARN stands for Amazon Resource Name, and it's basically like this global identifiable for any resource by Amazon. It could be a bucket, an instance, a role, a queue, a table, a Lambda function. A example:
arn:partition:service:region:account-id:resource

What is S3? 
Amazon S3 (Simple Storage Service) is AWS’s scalable object storage service used to store and retrieve data from anywhere on the internet. It stores data as objects inside buckets and supports virtually unlimited storage capacity with high durability, security, and availability. Amazon S3 also provides multiple storage classes to optimize cost based on data access patterns.

What is a Bucket?
A container for objects. Objects contains keys, values, metadata, and size. 

Policies Explaination 
The "S3UploaderOnly-Simon" policy explictly states only allowed actions. It first starts with the "Statement": [ ... ]", which intializes an array of permission rules. Followed by it, is ""Action": ["s3:PutObject", "s3:GetObject"]". Since the Effect is "Allow", everything is denied, as only the explicit Allow is allowed. (IAM is deny by default) 

We scoped it that way because we wanted to only allow the account to upload and get from the buckets. We wouldn't want  users with write access to our storage. 


