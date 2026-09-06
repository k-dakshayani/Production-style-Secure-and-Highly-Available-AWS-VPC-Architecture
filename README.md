Secure & Highly Available AWS VPC Architecture

A production-style AWS network architecture demonstrating secure, highly available application deployment using a custom VPC, public/private subnet isolation, a bastion host, an Auto Scaling Group, and an Application Load Balancer.

Architecture Diagram
<img width="1057" height="846" alt="image" src="https://github.com/user-attachments/assets/9255f5fb-ebbb-4044-a422-8bf0f8765a0f" />



Diagram illustrates the VPC design across two Availability Zones, with public subnets hosting the Load Balancer and Bastion Host, and private subnets hosting the Auto Scaling Group of application servers.

Overview

This project provisions a custom VPC spanning two Availability Zones, with public and private subnets in each. Application servers are deployed in the private subnets — never directly exposed to the internet — and are reached only through an internet-facing Application Load Balancer. A bastion host in the public subnet provides the sole SSH entry point for administrative access to the private instances.

Architecture Components
Component	Description
VPC	Custom VPC with a defined CIDR block, spanning 2 Availability Zones
Public Subnets (x2)	One per AZ — host the Application Load Balancer and Bastion Host
Private Subnets (x2)	One per AZ — host the application EC2 instances (no direct internet access)
Internet Gateway	Provides internet connectivity to resources in the public subnets
Route Tables	Separate public and private route tables to control traffic flow
Bastion Host	EC2 instance in a public subnet used as a secure SSH jump box into private instances
Auto Scaling Group	Maintains 2 EC2 instances running the web app across both private subnets
Target Group	Registers the private EC2 instances on port 8000 for the load balancer
Application Load Balancer	Internet-facing ALB; HTTP listener on port 80 forwards to instances on port 8000
Security Groups	Enforce least-privilege access: ALB → instances, bastion → instances
Traffic Flow

Application traffic:

User → ALB (port 80) → Target Group → Private EC2 Instance (port 8000, Python HTTP server)

Administrative access:

Admin → SSH → Bastion Host (public subnet) → SSH → Private EC2 Instance
Security Design
Private EC2 instances have no public IP and are unreachable directly from the internet
Bastion host security group: allows inbound SSH (22) only from a trusted admin IP
Private instance security group: allows inbound SSH only from the bastion's security group, and inbound HTTP (8000) only from the ALB's security group
ALB security group: allows inbound HTTP (80) from the internet
What This Project Demonstrates
Custom VPC design with subnet isolation across multiple Availability Zones
High availability through multi-AZ Auto Scaling and load balancing
Secure administrative access patterns using a bastion host
Least-privilege security group configuration between architecture tiers
End-to-end request flow from a public load balancer to a private backend
Known Limitations / Next Steps

This build reflects core VPC and high-availability networking concepts. To harden it further toward production use:

 Add a NAT Gateway in each public subnet so private instances can reach the internet for OS/security updates
 Add an HTTPS listener (port 443) with an ACM certificate, redirecting HTTP → HTTPS
 Replace Python's built-in http.server with a production WSGI server (e.g., Gunicorn) behind Nginx
 Replace the bastion host with AWS Systems Manager Session Manager for keyless, auditable access
 Add CloudWatch alarms for instance health, ALB errors, and Auto Scaling events
 Manage infrastructure with Terraform or CloudFormation for repeatable, version-controlled deployments
Tech Stack

AWS VPC EC2 Auto Scaling Group Application Load Balancer Target Groups Security Groups Internet Gateway Route Tables Bastion Host Python

Result

The application was successfully accessed via the Application Load Balancer's DNS name, confirming correct routing from the public internet through to the private, load-balanced application tier.
