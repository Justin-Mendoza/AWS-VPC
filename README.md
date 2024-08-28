# AWS-VPC
Created a VPC infrastructure using AWS with security features such as Security Groups, ACLs, etc. Created public and private subnets and tested intra-subnet connectivity as well as VPC connectivity with other clouds using EC2 instances. Implemented VPC Peering and monitored traffic flow using AWS Cloudwatch.

Project Overview:
Secure AWS VPC Design and Implementation
The goal of this project was to understand the basics of cloud architecture on a more macro level, and familiarize myself with AWS' cloud services.
Architecture Diagram:
VPC 1: Resource Map
<img width="1121" alt="Screenshot 2024-08-28 at 1 55 10 PM" src="https://github.com/user-attachments/assets/8febbf13-081a-4cb4-90d1-e420fedd72dc">
VPC 2: Resource Map
<img width="1122" alt="Screenshot 2024-08-28 at 1 55 54 PM" src="https://github.com/user-attachments/assets/c583a23c-117d-459e-9664-f96e36a952a1">

Peering connection between both VPCs.
<img width="963" alt="Screenshot 2024-08-28 at 1 57 01 PM" src="https://github.com/user-attachments/assets/98f964a0-3038-4a0a-8d8b-84def2c68f90">


Process:

1. AWS Account: An AWS account is necessary for this project to create a VPC and access all of the AWS resources.
2. AWS Services: EC2, VPC, CloudWatch, and S3.
3. VPC Design and Configuration:
VPC Creation steps:
To create a VPC, We need to access AWS' VPC console, Where we can create the VPC, public and private subnets (with different CIDR blocks), Route tables and network connections.
AWS' abstracts this process very easily, so it could be done all in one shot under "Create VPC and more, just need to select how many subnets (and their CIDR blocks), availability zones and  their endpoints.

  Security Groups and Network ACLs:
Security groups and Netwrok ACLs are what is used to choose who and what can access the resources in your VPC.

Network ACLs are a set of general rules that dictates what kind of data
comes through that is enforced on the entire subnet instead of on an
individual level. For example, a Network All could enforce what IP address a
whole subnet refuses.

Security groups are a set of rules on a resource level that can limit what
addresses can send and receive to this resource, as well as what processes
or protocols can access the resource.

The difference between ACL's and SGs:
Security groups act on an individual level, enforcing rules on the specific
resource, for example it can specify which port or protocols are allowed on
the specific resource. Network ACLs are on the whole subnet, controlling
denying certain addresses.

There is also a need to write the inbound and outboubnd rules. this will choose which IP address our VPC can send to adn receive from.

  Route Tables:
Route Tables are a set of rules that direct traffic and tells it where to go. If the
direction of the data is on route table CIDR, then it will send to the vpc, if not,
then it will send it back out the internet.
Route tables are needed to make a subnet public because without route
tables ,the data coming from the subnet wouldn't know where to send it, and
the data coming into the subnet wouldn't know where to receive data

  EC2 Instances Deployment
Instance Launch:
EC2 is a Virtual machine by AWS, that can be accessed from anywhere. We will use EC2 in our VPC to test connectivity.


Directly accessing a virtual machine means having access to use the
operating system virtually. So like using a computer over the net without
having the actual computer in front of you.

To access our VM, SSH is a protocol that validates that the user is authorized to use this VM.

For SSH, we need to create a key pair.

Key pairs are a way for helps software engineers access their virtual
computer. One public key kept in the cloud, and one private key kept on the
user's computer are used in order to validate a user and give them access.
A private key's file format means the type of file that the key will be kept in,
like .PDF, .docx, etc. But specifically for key pair generation.

(It's important to note that AWS uses EC2 Connect which automated the Keypair creation and management for us.)

To launch an EC2 instance, You go to the EC2 portal and launch a new EC2 Instance.
This will give you the choice of where to put this EC2 instance, We will place one instance in the public and private subnet.

<img width="697" alt="Screenshot 2024-08-28 at 2 18 24 PM" src="https://github.com/user-attachments/assets/8105999d-929d-4811-b9e9-914ead5f9c87">


I connected to my EC2 instance using EC2 Instance Connect, which is a mechanism that simplifies the ssh connection process, letting me connect while EC2 instance connect creates and manages the key pairs.
My first attempt at getting direct access to my public server resulted in an error, because the public security group was accepting http inbound traffic but not ssh traffic which is what we use to connect to our virtual machine.
I fixed this error by adding an inbound rule on my Private security group that accepts inbound SSH traffic, By default, security groups will allow http inbound connections, but not have anything set for SSH, so this is an important step to access EC2 instances.

To test connectivity, I use the ping command to send a message from my public Instance into my Private instance by specifying the address of my private EC2 Instance.

At first ther is no response. This is because our NACLs and Sgs do not allow for ICMP traffic. We modify this by adding inbound and outbound rules into our Sgs and NACLs.

We can also test connectivity to the internet by pinging or curling public domains.

  VPC Peering

We then establish a Peering conenctiong between our 2 VPCs by going into the Peering Connections tab and Creating a Peering Connection with either being the requester and the other being the accepter.
We then have to configure the Route tables so that we can receive and send to and from both VPCs.

  VPC Endpoints and Secure Resource Access

To configure my EC2 instances to use a VPC endpoint for secure access to AWS services, I began by creating the VPC endpoint from the VPC Dashboard. I selected the appropriate AWS service (e.g., S3), and configured the endpoint for my VPC, choosing the relevant subnets and security groups.
Initially, my instances were still routing traffic to the public internet. This issue arose because the route tables for the private subnets did not include a route for the VPC endpoint. To fix this, I updated the route tables to direct traffic destined for the service to the VPC endpoint.
I then verified the configuration by ensuring that the security groups attached to the instances allowed outbound traffic to the VPC endpoint and that the endpoint's security group permitted the necessary inbound traffic. After making these adjustments, I tested connectivity to confirm that the instances were using the VPC endpoint for service access.
By following these steps, I successfully routed traffic through the VPC endpoint, ensuring secure and private access to AWS resources without relying on public internet connections.

8. Monitoring and Logging

I set up Cloudwatch, which logs and follows traffic so that it is easy to track if ever there are any issues with the security group or NACLs.

Logs are a collection of what is happening in our environment. it is a list of all the error and problems that is happening with our VPCs so that we can more easily troubleshoot them.

Log groups are essentiallt a big folder with all the logs of a given environment. Each log monitors a specific environment.

To do this we first need to give permission to our VPC flow log to receive written logs from our Cloud. To do this we need to create an IAM role.
IAM roles will give permission to our resources to exchange data with eachother and communitcate with eachother.

So we create a custom Trust policy to give access to vpc-flow-logs.amazonaws.com

Then we can create the Flowlog on cloudwatch with this IAM role to monitor the VPC's traffic.

<img width="551" alt="Screenshot 2024-08-28 at 2 41 12 PM" src="https://github.com/user-attachments/assets/c4585e0b-5346-4500-bf35-f4a5eff74658">

We pinged one EC2 instance and we see that we received a response.
<img width="1198" alt="Screenshot 2024-08-28 at 2 43 02 PM" src="https://github.com/user-attachments/assets/51b100f2-78de-4f68-8e0e-76aeacca9a01">


In log Insights, we can run any of the queries in the right and get log data to see.

For example: 
<img width="1158" alt="Screenshot 2024-08-28 at 2 44 14 PM" src="https://github.com/user-attachments/assets/2218dbfb-1611-4433-b7e7-aac103d9affc">

This is a log breakdown of Top 10 byte transfers by source and destination IP addresses.
Log Insights is a tool that lets us track logs and analyze them as well as other things such as flitering logs and such.

Here we see the IP addresses that transfer the most into our VPC, and If we see an unknown IP address or and IP address we know we dont like, we can use route tables, SGs, NACLs and other security features to protect out VPC.


4. Conclusion

In establishing and configuring the AWS VPC, we successfully built a secure and efficient network infrastructure by leveraging AWS's comprehensive tools. The process began with creating the VPC and defining its components, including public and private subnets, route tables, and network connections. AWS's streamlined setup allowed for efficient configuration of these elements in one go.
Security was a primary focus, with careful setup of Security Groups and Network ACLs to manage access and enforce rules. Security Groups provided detailed, instance-level control, while Network ACLs applied broader, subnet-wide rules, ensuring robust traffic management and protection.
Route tables played a crucial role in directing traffic appropriately within the VPC and to the internet. We ensured that these tables were configured to make subnets public or private as needed, facilitating proper data routing.
The deployment of EC2 instances in both public and private subnets enabled us to test connectivity and access. Using EC2 Instance Connect and managing SSH key pairs facilitated secure access to these instances, while adjustments to security group rules and Network ACLs were made to ensure proper communication.
A VPC peering connection was established between two VPCs, enabling seamless inter-VPC communication. Route tables were updated to support this connectivity, enhancing our network's flexibility and reach.
We also implemented VPC endpoints to securely access AWS services like S3, avoiding public internet traffic. By configuring these endpoints and updating route tables and security groups, we ensured private, secure access to essential resources.
Finally, monitoring and logging were set up using CloudWatch to keep track of VPC traffic. By configuring VPC Flow Logs and creating an IAM role for permissions, we could effectively monitor and analyze network activity. CloudWatch Logs Insights provided valuable insights into traffic patterns, helping us troubleshoot and secure the network.
Overall, this setup created a robust VPC environment with secure, efficient access to AWS resources, comprehensive network management, and effective monitoring and logging capabilities.
