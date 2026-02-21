**VPC in Production like Project**

Create VPC that you can use for servers in Production

- To improve resiliency deploy servers in 2 availability zones using autoscaling and Application load balancers

- Deploy servers in private subnets

- Servers receive request through load balancers

- Servers connect to internet using NAT gateway

- To improve resiliency deploy NAT gateway in both Availabitiy zones

![](media/image1.png){width="6.268055555555556in" height="3.232638888888889in"}

![](media/image2.png){width="2.454861111111111in" height="1.9388888888888889in"}**Create VPC**

VPC and More

Name = PVCProdExample

IPv4 CIDR block = 10.0.0.0/16

IPv6 = No

No. of availability zones = 2

![](media/image3.png){width="1.7673611111111112in" height="1.0708333333333333in"}No. of Public subnets = 2

No. of Private subnets = 2

NAT gateway = 1 per Availability zone

VPC end Point = No

![](media/image4.png){width="6.268055555555556in" height="0.8076388888888889in"}

**AutoScaling Group**

Create Autoscaling group

- Create launch template

  - AMI -- Ubuntu

  - Instance type -- t2micro

  - Keypair

  - Security group

    - Select vpc created

    - Ssh from anywhere

    - Port 8000 from anywhere

  - Launch template

- Select launch template

- Select VPC

- Select 2 Private subnets

- No Load Balancer

- Size for autoscaling

  - Min -- 1

  - Max -- 4

  - Desired -2

- Verify if both instances are created in separated private subnets

![](media/image5.png){width="6.268055555555556in" height="0.875in"}  
![](media/image6.png){width="6.268055555555556in" height="0.7680555555555556in"}

**Install Application inside server in private subnets\[Mostly No Public IPs\]**

- To connect to server in private host we need **bastion host\[acts as a mediator between external users or public subnets\]**

**Create Bastion host \[ec2 instance with public IP\]**

- Ubuntu image

- Same VPC as servers but in public subnet

- Enable public IP

- Ssh port expose in security group

![](media/image7.png){width="6.268055555555556in" height="1.1020833333333333in"}

- Copy key value to bastion host to connect to server instances

![](media/image8.png){width="6.268055555555556in" height="0.4618055555555556in"}

From bastion host login to servers/instances in private subnet using private IP and .pem file

![](media/image9.png){width="6.268055555555556in" height="1.0993055555555555in"}

After logging in to server, create a simple html index file  
and run server with this index file using python

![](media/image10.png){width="6.268055555555556in" height="0.7861111111111111in"}

Now server is running in one of the server in private subnet

**Load Balancer**

Now load balancer will route traffic to only healthy servers, so only 1 server receives all the traffic.

If it routes to other server we get server error

Create load balancer and attach 2 instances of private subnet as target group

Loadbalancer under EC2

Manage HTTP traffic from user:80 to server:8000

- Load balancer IP address type -- IPv4

- Select VPC created

- Select 2 availability zones

- Select security group

- Listening and routing -- HTTP = 80

- **Create target group**

  - Target type = instances

  - HTTPS protocol = 8000

  - Select Created VPC

  - Select 2 server instances and include as pending

- Select created target group

- Create loadbalancer

Now HTTP port 80 will be not reachable because we have not allowed in security group, edit and allow HTTP port 80

![](media/image11.png){width="6.268055555555556in" height="0.8861111111111111in"}

Now get the load balancer DNS endpoint and try to access server running

![](media/image12.png){width="6.268055555555556in" height="1.5590277777777777in"}

Output from application running in server1\[private subnet 1\]

![](media/image13.png){width="6.268055555555556in" height="1.242361111111111in"}

Output from application running in server2\[private subnet 2\]

![](media/image14.png){width="6.268055555555556in" height="1.7972222222222223in"}
