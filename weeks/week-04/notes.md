Concepts: S3 storage classes (Standard, IA, Glacier), lifecycle rules, versioning; RDS Multi-AZ; DynamoDB partition keys and capacity modes.

S3 Storage Classes
We know that Amazon S3 (Simple Storage Service) is AWS’s scalable object storage service used to store and retrieve data from anywhere on the internet. However, what if you want to adjust how fast you can get data back versus how much it costs to keep it. Cheaper classes aren’t just “slower.” They move cost from storage to access:

Standard: you pay to store, retrieval is basically free
Colder classes: you pay little to store, but you pay per GB retrieved

S3 classes map onto that almost one-to-one:

Class	
Standard	
Standard-IA	
Glacier Instant Retrieval	
Glacier Flexible	
Glacier Deep Archive

Lifecyle Rules
A standing instruction attached to a bucket: “when an object reaches age X, do Y to it.” AWS runs it automatically once a day in the background. You set it once and never touch it again. There's two type: Transition — move the object to a cheaper storage class or Expiration — delete the object

Versioning
Versioning keeps every version of an object instead of overwriting it. It’s a bucket-level setting, off by default. Think of it like version control, you can revert back changes. But one thing: you still get charged for past versions. 

RDS Multi-AZ (Relational Database Service - Availability Zone)
Multi-AZ is RDS’s high-availability feature. AWS keeps a duplicate of your database in a second Availability Zone and fails over to it automatically if the primary dies. You're essentailly paying for a insurance or a standby backup of your database. For instance, Amazon Web Services (AWS) has a total of 9 Availability Zones (AZs) across its primary US East regions. If you want something to maybe offload the amount of traffic, consider using a read replica server: a copy of your database that can answer queries but can’t accept writes. However, this is async which means it won't wait for the main server to send/receive the most current information, even if it's delayed by 1-2 seconds.

DynamoDB Partition keys and Capacity modes
First of all DynamoDB is a NoSQL key-value database. No tables with relationships, no joins, no SQL. You store items (like JSON documents) and fetch them by key. It scales to any size with consistent single-digit-millisecond latency, which relational databases can’t promise.

A partition key is a database attribute or column used to determine how data is distributed across physical storage nodes, partitions, or brokers. It solves the problem: Your data is too big for one machine, so it’s spread across many. But then how do you find anything? Searching every machine would be slow. 

The solution: DynamoDB uses the partition key as an address. It runs the key through a math function that always gives the same answer for the same input, and that answer says which machine holds the item.

So writing and reading both work the same way: hand over the key, get pointed at one machine, done. It never searches. That’s why it’s fast whether you have 100 items or 100 billion — it’s always one lookup.

In AWS, the partition key comes directly from the data attributes defined by you, the user, when creating a table or configuring a service. It is not generated automatically by AWS; rather, it is an application-driven choice made during data modeling.  DynamoDB takes the value of this key and inputs it into an internal hash function. The output of this function determines the physical storage partition where that specific item is stored and retrieved

NoSQL is a umbrella term for databases that aren’t relational. DynamoDB is key-value: you store items and fetch them by key. Items in the same table can have different attributes. No joins. Examples: DynamoDB, MongoDB, Cassandra, Redis.








RDS MySQL





