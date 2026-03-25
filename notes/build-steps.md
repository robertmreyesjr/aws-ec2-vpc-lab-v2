# Build Steps – AWS EC2 + VPC Lab v2

## What did you build?

I built a custom AWS network environment using Amazon VPC with both a public subnet and a private subnet. I launched one EC2 instance in the public subnet and one EC2 instance in the private subnet, then configured internet access using an Internet Gateway and a NAT Gateway.

The public EC2 instance was configured as a web server and accessed through its public IP address. The private EC2 instance had no public IP and could only be accessed from inside the VPC through the public EC2 instance.

---

## Why did you build it?

I built this lab to strengthen my hands-on AWS networking and compute skills beyond a basic single-instance deployment. I wanted to better understand the difference between public and private subnets, how NAT Gateway supports outbound connectivity for private instances, and why private resources are not directly reachable from the public internet.

I also built this as a GitHub portfolio project to demonstrate practical AWS architecture, validation, and documentation skills.

---

## What AWS services were involved?

The following AWS services and components were used in this lab:

- **Amazon VPC** – provided the isolated network environment
- **Public Subnet** – hosted the internet-facing EC2 instance and NAT Gateway
- **Private Subnet** – hosted the internal EC2 instance without a public IP
- **Internet Gateway** – enabled public internet connectivity for the VPC
- **NAT Gateway** – allowed the private EC2 instance to reach the internet for outbound traffic
- **Elastic IP** – attached to the NAT Gateway
- **Route Tables** – controlled subnet traffic paths
- **Security Groups** – controlled inbound and outbound traffic to the EC2 instances
- **Amazon EC2** – provided the compute instances
- **EC2 Key Pair** – allowed secure SSH access
- **Amazon Linux / Apache HTTP Server** – used to host a test web page on the public EC2 instance

---

## Environment Design

### Network Layout
- **VPC CIDR:** `10.0.0.0/16`
- **Public Subnet CIDR:** `10.0.1.0/24`
- **Private Subnet CIDR:** `10.0.2.0/24`

### Routing Design
- **Public Route Table**
  - `0.0.0.0/0 -> Internet Gateway`
- **Private Route Table**
  - `0.0.0.0/0 -> NAT Gateway`

### EC2 Placement
- **Public EC2 Instance**
  - launched in the public subnet
  - assigned a public IP
- **Private EC2 Instance**
  - launched in the private subnet
  - no public IP assigned

---

## Build Process

### 1. Created the VPC
I created a custom VPC with the CIDR block `10.0.0.0/16`.

### 2. Created the public subnet
I created a public subnet with the CIDR block `10.0.1.0/24` and enabled auto-assign public IPv4 addresses.

### 3. Created the private subnet
I created a private subnet with the CIDR block `10.0.2.0/24` and left public IP auto-assignment disabled.

### 4. Created and attached the Internet Gateway
I created an Internet Gateway and attached it to the VPC so internet-bound traffic could reach public resources.

### 5. Created the public route table
I created a route table for the public subnet and added a default route:
`0.0.0.0/0 -> Internet Gateway`

I then associated the route table with the public subnet.

### 6. Allocated an Elastic IP and created a NAT Gateway
I allocated an Elastic IP and used it to create a NAT Gateway in the public subnet.

### 7. Created the private route table
I created a route table for the private subnet and added a default route:
`0.0.0.0/0 -> NAT Gateway`

I then associated the route table with the private subnet.

### 8. Created security groups
I created two security groups:

#### Public EC2 security group
Inbound rules:
- SSH (22) from My IP
- HTTP (80) from `0.0.0.0/0`

#### Private EC2 security group
Inbound rules:
- SSH (22) from the public EC2 security group

This allowed the private instance to be reached only from the public instance and not from the public internet.

### 9. Created a key pair
I created an EC2 key pair for SSH access.

### 10. Launched the public EC2 instance
I launched an Amazon Linux EC2 instance into the public subnet with a public IP and attached the public security group.

### 11. Launched the private EC2 instance
I launched another Amazon Linux EC2 instance into the private subnet without a public IP and attached the private security group.

### 12. Connected to the public EC2 instance
I connected to the public EC2 instance using SSH or EC2 Instance Connect.

### 13. Installed Apache on the public EC2 instance
I installed Apache on the public instance and created a simple test web page.

Commands used:

```bash
sudo yum update -y
sudo yum install -y httpd
sudo systemctl start httpd
sudo systemctl enable httpd
echo "<h1>Public EC2 is working</h1>" | sudo tee /var/www/html/index.html