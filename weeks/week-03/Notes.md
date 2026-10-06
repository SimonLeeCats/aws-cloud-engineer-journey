What is AMI? (Amazon Machine Image)
This is simply just a image of pre-installed OS with serveral software already installed on it. It's a snapshot of the a hardrive with an OS, and is served everytime a new instance starts. (Like Amazon Linux 2023)

What is EC2? (Elastic Compute Cloud)
This is an AWS serivice in where you rent virtual computers. Essentially, an instance is a VM launched from a AMI, and EC2 is the service that creates and manages these

Instance Types?
Since different sometimes need different resources, you create types when you deploy these AMI. Analyzing t3.micro, t is the family and means burstable. t generally means general purpose, meanwhile other families such as m, c, r have their own quirks such as being more compute heavy or memory heavy.

The number is the generation, the higher generation is usually preferred. Lastly, micro is the size. There's small, medium, etc... but we only need micro for this lab. 

What are VPCs? (Virtual Private Network)
"Amazon VPC (Virtual Private Cloud) is a networking service that lets you create a logically isolated virtual network within AWS. It gives you control over IP addresses, subnets, routing, and network access for your AWS resources." It essentially gives you a playground for you to subnet and all. However, the limit is /16. Every region comes with a default VPC (172.31.0.0/16), and if you don't pick anything, the EC2 launch wizard puts your instance there.

Imagine VPC as like the hotel, the subnets as the individual floors. Security Groups are protecting the apartments inside of those floors, and the NACLs are protecting the floors. 

Security Groups and NACLs  (Network Access Control List)
Security groups and NACLs are two seperate network controls. NACLs is defined for every VPC (Virtual Private Network) or for every subnet. Meanwhile, Security Groups are defined for only the instance network interface. 

Some important configuration details is that Security groups do Allow onlys, which means it has an implicit deny. NACL have Allow and Deny, and instead has a implicit allow. Additionally, NACLs are Stateless and Security Groups are stateful. 

What is EBS Volumes? (Elastic Book Store)
Every instance you deploy needs a disk or some storage, and what if you want the data in that disk to stay/outlive? You can attach this to a instance so it can outlast the instance. Even if you terminate the instance, the extra EBS volumes you attached aren't. 

What is Bootstapping
When you boot up the instance, what if you want to configure it without having to manually do it. This is called user data bootstrapping, and it's essentially adding a script you paste a launch time through a tool called clound-init. It only runs once. 

