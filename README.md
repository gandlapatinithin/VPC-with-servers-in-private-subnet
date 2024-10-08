# VPC-with-servers-in-private-subnet
This project showcases the creation of a highly resilient and secure Virtual Private Cloud (VPC) designed for production environments. The architecture spans two Availability Zones to ensure high availability and includes Auto Scaling groups and an Application Load Balancer (ALB) to provide scalability and fault tolerance. For enhanced security, the application servers are deployed in private subnets, with external traffic routed through the ALB in the public subnets.

To enable outbound internet access from the private servers, a Network Address Translation (NAT) gateway is deployed in each Availability Zone, ensuring availability and fault tolerance. Public subnets host both the NAT gateways and the load balancer, while private subnets house the server instances.

Additionally, a Bastion host is deployed in a public subnet to facilitate secure communication with the private servers. Administrators can connect to the Bastion host to access instances in the private subnets, ensuring secure management without exposing private servers directly to the internet. This architecture balances security, scalability, and resilience for a robust production environment.

**Steps 1. Create the VPC**
Use the following procedure to create a VPC with a public subnet and a private subnet in two Availability Zones, and a NAT gateway in each Availability Zone.

1)To create the VPC
  a)Open the Amazon VPC console at https://console.aws.amazon.com/vpc/.

  b)On the dashboard, choose Create VPC.

  c)For Resources to create, choose VPC and more.

2)Configure the VPC

  d)For IPv4 CIDR block, you can keep the default suggestion, or alternatively you can enter the CIDR block required by your application 
    or network.

  e)If your application communicates by using IPv6 addresses, choose IPv6 CIDR block, Amazon-provided IPv6 CIDR block.

3)Configure the subnets

  f)For Number of Availability Zones, choose 2, so that you can launch instances in multiple Availability Zones to improve resiliency.

  g)For Number of public subnets, choose 2.

  h)For Number of private subnets, choose 2.

  g)You can keep the default CIDR block for the public subnet, or alternatively you can expand Customize subnet CIDR blocks and enter a 
    CIDR block. For more information, see Subnet CIDR blocks.

  i)For NAT gateways, choose 1 per AZ to improve resiliency.

4)For VPC endpoints, if your instances must access an S3 bucket, keep the S3 Gateway default. Otherwise, instances in your private subnet can't access Amazon S3. There is no cost for this option, so you can keep the default if you might use an S3 bucket in the future. If you choose None, you can always add a gateway VPC endpoint later on.

  j)Choose Create VPC.

**step 2.Creating a Launch Template for an Auto Scaling Group**

  Before setting up an Auto Scaling group, you need to create a launch template that includes all the necessary configuration information to launch EC2 instances, such as the Amazon       Machine Image (AMI) and instance type. Follow these steps to create a launch template using the AWS console.

  a) Steps to Create a Launch Template
  
    *Open the EC2 Console: Go to the Amazon EC2 console at https://console.aws.amazon.com/ec2/.
    *Navigate to Launch Templates: From the navigation pane, under "Instances," select Launch Templates and click Create launch template.
    *Configure Basic Information:
    *Name: Enter a name, such as aws-prod-project.
    *Description: Provide a description, such as "Proof of concept for app deployment in AWS private subnet."
    *Set Launch Template Details: Under "Launch template contents," fill in the following:
    *Amazon Machine Image (AMI): Choose the AMI ID, e.g., Ubuntu. You can browse recent or Quick Start AMIs.
    *Instance Type: Choose t2.micro for free tier eligibility.
    *Key Pair: Select an existing key pair or create a new one for SSH access.
    *Security Groups: Select one or more security groups to define network access to the instances.
    *Network Interface: Optionally adjust the default network settings, like enabling/disabling public IPv4 addresses.
    *Create the Launch Template: After configuring all settings, click Create launch template.
    *Proceed to Auto Scaling Group: On the confirmation page, choose Create Auto Scaling group to proceed with setting up the Auto Scaling group.

**step 3.Creating an Auto Scaling Group Using a Launch Template**

    *Once the launch template is created, follow these steps to set up an Auto Scaling group that automatically manages instance scaling based on demand.
    *Steps to Create an Auto Scaling Group
    *Open the EC2 Console: Go to the Amazon EC2 console at https://console.aws.amazon.com/ec2/, and select Auto Scaling Groups from the navigation pane.
    *Select Region: Ensure the AWS Region matches the one where you created the launch template.
    *Create Auto Scaling Group: Click Create an Auto Scaling group.
    *Choose Launch Template:
    *Name: Enter a name for the Auto Scaling group, such as aws-prod-project.
    *Launch Template: Select the launch template created earlier.
    *Launch Template Version: Choose whether to use the default, latest, or a specific version of the launch template.
    *Instance Launch Options:
    *Instance Type: Skip if you're using the instance type specified in the launch template.
    *VPC: Select the same VPC as the one defined in your launch template.
    *Subnets: Choose private subnets in multiple Availability Zones for high availability.
    *Load Balancer: You can skip this if no load balancer is needed.
    *Health Checks: Optionally, enable EBS health checks and define a grace period for instance health checks.
    *Monitoring: Enable Amazon CloudWatch group metrics collection, if needed.
    *Group Size and Scaling Policies (Optional):
    *Desired Capacity: Set the initial number of instances to launch and for this desired capacity is 1 per AZ.
    *Scaling Limits: Adjust the minimum and maximum capacity based on your needs .
    *Scaling Policy: Choose whether to create a target tracking scaling policy (you can create it later as well).
    *Review and Create: Review all the settings and click Create Auto Scaling group to complete the setup.

**This step-by-step process sets up a highly available and scalable infrastructure by leveraging launch templates and Auto Scaling groups in AWS.**

**Steps to Create a Bastion Host and Access EC2 Instances in a Private Subnet**

1. Launch the Bastion Host EC2 Instance
   *Name: bastion-host
   *AMI: Ubuntu
   *Instance Type: t2.micro (free tier eligible)
   *Key Pair: Select or create a key pair, e.g., nithin.pem
   *Network Settings:
   *Security Group: Create a new security group allowing SSH access.
   *Allow SSH traffic from: Anywhere (0.0.0.0/0)
   *VPC: Select the same VPC, e.g., aws-prod-vpc
   *Auto-assign Public IP: Enable
   *Once the settings are configured, click Launch Instance.

2. Retrieve the Necessary IP Addresses
 *Bastion Host Public IPv4 Address: 2.485.235.124
 *First Instance Private IPv4 Address: 10.0.164.213
 *Second Instance Private IPv4 Address: 10.0.172.117

4. Copy Key Pair to Bastion Host
   
  *Using your terminal or command prompt, copy the nithin.pem file to the Bastion host:

   *For Linux/macOS:
    bash:
    scp -i nithin.pem nithin.pem ubuntu@(public-ip):/home/ubuntu/

   *For Windows:
    bash:
    scp -i C:/Users/reddy/Downloads/nithin.pem C:/Users/reddy/Downloads/nithin.pem ubuntu@(public-ip):/home/ubuntu
    *When prompted, type "yes" to accept the connection.

4. SSH into the Bastion Host
   *Once the key pair is copied, SSH into the Bastion host using the following command:
   bash:
   ssh -i nithin.pem ubuntu@(private-ip-1)

   *After successful login, list the files in the directory to confirm the key pair was transferred:
    bash
    ls
   *You should see nithin.pem in the directory.

6. Access the First Instance (Private Subnet)
   *Now, use the Bastion host to SSH into the first private EC2 instance:
    bash
    ssh -i nithin.pem ubuntu@(private-ip)
   *When prompted, type "yes" to accept the connection.

6. Modify the HTML File on the First Instance
   
   *Edit the index.html file on the first instance:
    bash
    vim index.html
    *Enter insert mode by pressing i, then add the following content:

    html:

   <!DOCTYPE html>
   <html>
   <body>

   <h1>My First AWS PROJECT demonstrating apps in a private subnet</h1>
   <p>Congratulations on first instance!!!</p>

   </body>
   </html>
   *To save and exit the file, press Esc, type :wq!, and press Enter.

7. Start a Simple HTTP Server
   
   *To serve the HTML page, start a simple HTTP server on port 8000:
    bash:
   python3 -m http.server 8000
   *You can now use your Bastion host to access and manage instances within the private subnet, and your first instance is serving a basic HTML webpage.

   *This setup ensures that the Bastion host provides secure SSH access to private instances, while your web server is isolated from direct internet access.

   *Steps to Create an Application Load Balancer (ALB) and Attach EC2 Instances as Target Group
1. Create an Application Load Balancer (ALB)
   *Open the AWS Management Console

   *Go to the Load Balancer section under EC2.
     Create a New Load Balancer
     Click on Create Load Balancer.
     Load Balancer Type: Select Application Load Balancer (ALB).
     Name: Enter aws-prod-project.
     Scheme: Choose Internet-facing.
     IP Address Type: Choose IPv4.
2. Configure Network Mapping
     VPC: Select aws-prod-vpc.
     Mappings: Select both Availability Zones, and make sure you choose the PUBLIC subnets for each zone.
3. Security Group
     Select or create a security group, e.g., aws-prod-project that allows necessary traffic (like HTTP on port 80).
4. Listeners and Routing
     Protocol: Select HTTP.
     Port: Set to 80.
     Default Actions: Create a new target group.
     Target Group Type: Select Instances.
     Target Group Name: Enter aws-prod-project.
     Protocol: Set to HTTP.
     Port: Set to 8000.
     VPC: Select aws-prod-vpc.
     Health Check: Use default values.
5. Register EC2 Instances to the Target Group
    Available Instances: Select the following instances:
    First Instance (private IP:  10.0.172.117)
    Bastion Host (public IP: 2.485.235.124)
    Second Instance (private IP: 10.0.164.213)
    Port: Set to 8000.
    Click Include as Pending Below and then Create Target Group.
6. Finalize the Load Balancer Setup
   Go back to the Load Balancers section and select the ALB you just created (aws-prod).
   Scroll down to the Listeners section. If you see any errors, follow the next steps to resolve them.
7. Fix Listener Security Group Inbound Rules
   Click on Security Groups and edit the inbound rules for aws-prod security group.
   Add the following rule:
   HTTP: Allow from Anywhere (0.0.0.0/0).
   Save the Rules.
8. Access the Application via Load Balancer
   Go back to the Load Balancer page, and copy the DNS name of the ALB.
   Open the DNS name in a new browser tab.
   If everything is configured correctly, you will see the web application from the EC2 instances deployed in private subnets 1 and 2 through the ALB.

**Congratulations! You have successfully demonstrated the deployment of applications across multiple private subnets behind an Application Load Balancer.**
