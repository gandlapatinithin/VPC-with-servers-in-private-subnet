# VPC-with-servers-in-private-subnet
This project showcases the creation of a highly resilient and secure Virtual Private Cloud (VPC) designed for production environments. The architecture spans two Availability Zones to ensure high availability and includes Auto Scaling groups and an Application Load Balancer (ALB) to provide scalability and fault tolerance. For enhanced security, the application servers are deployed in private subnets, with external traffic routed through the ALB in the public subnets.

To enable outbound internet access from the private servers, a Network Address Translation (NAT) gateway is deployed in each Availability Zone, ensuring availability and fault tolerance. Public subnets host both the NAT gateways and the load balancer, while private subnets house the server instances.

Additionally, a Bastion host is deployed in a public subnet to facilitate secure communication with the private servers. Administrators can connect to the Bastion host to access instances in the private subnets, ensuring secure management without exposing private servers directly to the internet. This architecture balances security, scalability, and resilience for a robust production environment.

Steps 1. Create the VPC

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
