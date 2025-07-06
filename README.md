# azure-loadbalancer-setup
Azure Load Balancer with Web Servers Setup
This guide demonstrates how to quickly set up an Azure Load Balancer with two working web servers for high availability and load distribution.

Prerequisites
Azure subscription
Access to Azure Portal (portal.azure.com)
Basic understanding of Azure Virtual Machines and networking concepts
Architecture Overview
The setup includes:

2 Windows Server 2022 Virtual Machines
1 Virtual Network with custom subnet
1 Azure Load Balancer (Standard SKU)
Network Security Groups with HTTP access rules
IIS web servers on both VMs
Step-by-Step Implementation
1. Create Resource Group
Navigate to Azure Portal
Create a new Resource Group named demo-RG
2. Create First Virtual Machine (VM1)
Go to Virtual Machines → Create Azure Virtual Machine

Configure the following settings:

Resource Group: demo-RG
Virtual Machine Name: vm1
Region: East US
Image: Windows Server 2022 Datacenter
Size: Standard B2s (2 vCPUs, 4 GiB RAM)
Username: ELPadmin
Password: [Set secure password]
Disk Settings:

Disk Type: Standard HDD
Networking Configuration:

Create new Virtual Network: v-net-a
Address Space: 10.1.0.0/16
Subnet: subnet-1A
Subnet Range: 10.1.0.0/24
Management:

Enable Auto-shutdown at 7:00 PM Eastern Time
Click Review + Create → Create

3. Create Second Virtual Machine (VM2)
Create another Virtual Machine with similar configuration:

Resource Group: demo-RG
Virtual Machine Name: vm2
Region: East US
Image: Windows Server 2022 Datacenter
Size: Standard B2s (2 vCPUs, 4 GiB RAM)
Username: ELPadmin
Password: [Same password as VM1]
Networking:

Use existing Virtual Network: v-net-a
Subnet: subnet-1A
Management:

Enable Auto-shutdown at 7:00 PM Eastern Time
4. Configure Virtual Machines
After both VMs are created, note their private IP addresses:

VM1: 10.1.0.4
VM2: 10.1.0.5
Install IIS Web Server on Both VMs
Connect to each VM via RDP
Open Control Panel → Switch to Large Icons
Disable Windows Defender Firewall (for demo purposes)
Open Server Manager → Add Roles and Features
Install Web Server (IIS) role
Complete installation on both VMs
Customize Web Pages
Navigate to C:\inetpub\wwwroot\
Edit iisstart.htm file
VM1: Add text "This is VM1"
VM2: Add text "This is VM2"
5. Create Azure Load Balancer
Go to All Services → Search for Load Balancers
Click Create Load Balancer
Configure settings:
Resource Group: demo-RG
Name: ELP-load-balancer
Region: East US
SKU: Standard
Type: Public
Frontend IP Configuration
Name: frontend-address
IP Type: Public IP
Public IP: Create new
Name: frontend-pip
SKU: Standard
Backend Pool Configuration
Name: backend-pool
Add both virtual machines (VM1 and VM2)
Include their IP configurations
Load Balancing Rules
Name: inbound-rule
Frontend IP: frontend-address
Backend Pool: backend-pool
Protocol: TCP
Port: 80
Backend Port: 80
Health Probe Configuration
Name: HTTP-probe
Protocol: HTTP
Port: 80
Path: /
Interval: 5 seconds
Unhealthy Threshold: 2
6. Configure Network Security Groups
For VM1:
Go to VM1 → Networking
Click Add Inbound Port Rule
Configure:
Source: Any
Destination: Any
Service: HTTP
Port: 80
Action: Allow
Priority: 310
Name: allow-HTTP
For VM2:
Repeat the same process for VM2
Add identical HTTP inbound rule
7. Test Load Balancer
Navigate to Resource Groups → demo-RG
Click on frontend-pip (Public IP)
Copy the public IP address
Open web browser and navigate to http://[public-ip-address]
Refresh the page multiple times to see load balancing between VM1 and VM2
Verification
Access the load balancer's public IP address
You should see alternating responses from VM1 and VM2
Each refresh may show different server responses
Health probe ensures only healthy servers receive traffic
Architecture Benefits
High Availability: Traffic distributes across multiple servers
Scalability: Easy to add more backend servers
Health Monitoring: Automatic failover if servers become unhealthy
Cost Effective: Standard load balancer with basic web server setup
Cleanup
To avoid ongoing charges:

Delete the Resource Group demo-RG
This will remove all associated resources
Verify auto-shutdown is configured for cost control
Additional Considerations
For production environments, enable Windows Firewall with specific rules
Consider using Azure Application Gateway for Layer 7 load balancing
Implement proper SSL/TLS certificates for HTTPS traffic
Use Azure Monitor for detailed performance monitoring
Troubleshooting
Ensure both VMs have IIS installed and running
Verify Network Security Group rules allow HTTP traffic
Check health probe status in load balancer configuration
Confirm virtual machines are in the same virtual network
