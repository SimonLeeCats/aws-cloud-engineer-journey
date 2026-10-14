While running all of these commands I noticed that the server name is always included. Like running a command such as aws s3 always made specify the operation. 

aws <service> <operation> [options]

aws s3 ls
aws ec2 describe-instances
aws iam list-users
aws dynamodb list-tables

S3: Object storage, accessed over HTTP
You’re storing whole files you won’t edit in place like backups, logs, images, build artifacts, static sites, etc... 

EBS: A virtual hard drive attached to one EC2 instance
You need a disk for a server to boot from or write to, or just as an backup (One instance)

EFS: A shared network filesystem (NFS)
Several EC2 instances need to read and write the same files at the same time, including shared uploads, a common config directory

RDS: Managed relational database (MySQL, PostgreSQL, etc.)
Your data has relationships, you need joins, transactions, and more consistency 

DynamoDB: Managed NoSQL key-value store
You know your access patterns, you look things up by key, and you need predictable low latency at any scale

