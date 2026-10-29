VPC Nexus

AWS Multi-VPC Network Architecture and Connectivity Management System

Tagline: Connect two VPCs. Prove private connectivity.

---

📌 Project Overview

VPC Nexus is an AWS networking project that demonstrates secure communication between two isolated Virtual Private Clouds (VPCs) using VPC Peering.

The project implements public and private subnets, EC2 instances, route tables, security groups, an Internet Gateway, and private IP-based connectivity.

The main objective is to establish and verify controlled communication between private resources across two VPCs while maintaining network isolation.

---

🏗️ Architecture

                         INTERNET
                             │
                     Internet Gateway
                             │
              ┌──────────────┴──────────────┐
              │           VPC 1             │
              │       10.0.0.0/16           │
              │                             │
              │  Public Subnet              │
              │  10.0.1.0/24                │
              │       │                     │
              │      EC2-A                  │
              │   Bastion Host              │
              │                             │
              │  Private Subnet             │
              │  10.0.2.0/24                │
              │       │                     │
              │      EC2-B                  │
              └──────────────┬──────────────┘
                             │
                       VPC PEERING
                             │
              ┌──────────────┴──────────────┐
              │           VPC 2             │
              │       10.1.0.0/16           │
              │                             │
              │  Private Subnet             │
              │  10.1.1.0/24                │
              │       │                     │
              │      EC2-C                  │
              └─────────────────────────────┘

---

☁️ AWS Resources

VPCs

Resource| CIDR
VPC-Nexus-Primary| "10.0.0.0/16"
VPC-Nexus-Secondary| "10.1.0.0/16"

Subnets

Subnet| CIDR| Type
VPC1-Public-Subnet| "10.0.1.0/24"| Public
VPC1-Private-Subnet| "10.0.2.0/24"| Private
VPC2-Private-Subnet| "10.1.1.0/24"| Private

EC2 Instances

Instance| Private IP| Role
VPC1-Public-EC2-A| "10.0.1.35"| Public / Bastion
VPC1-Private-EC2-B| "10.0.2.169"| Private EC2
VPC2-Private-EC2-C| "10.1.1.202"| Private EC2

The two private EC2 instances do not have public IPv4 addresses.

---

🔗 VPC Connectivity

The two VPCs are connected using:

VPC1 → VPC Peering → VPC2

Peering connection:

"VPC1-VPC2-Peering"

The required routes were added to the route tables:

VPC1 Route

10.1.0.0/16 → VPC1-VPC2-Peering

VPC2 Route

10.0.0.0/16 → VPC1-VPC2-Peering

This allows traffic between the two VPC CIDR ranges through the private network.

---

🔐 Security Controls

Security Groups were configured to control SSH and ICMP traffic.

VPC1 Public Security Group

- SSH — restricted to the user's public IP
- HTTP — allowed as required
- ICMP — allowed for required connectivity testing

VPC1 Private Security Group

- SSH from the VPC1 public subnet
- ICMP from the VPC1 public subnet

VPC2 Private Security Group

- SSH from VPC1
- ICMP from VPC1

Outbound traffic is allowed as required for the lab configuration.

---

🧪 Connectivity Testing

The project was verified using multiple connectivity tests.

1. Forward Connectivity

From the VPC1 private instance:

ping -c 4 10.1.1.202

Result: 4 packets received, 0% packet loss.

This confirmed connectivity from VPC1 to the private instance in VPC2.

2. Reverse Connectivity

From the VPC2 private instance:

ping -c 4 10.0.2.169

Result: 4 packets received, 0% packet loss.

This confirmed reverse private connectivity.

3. Private SSH Connectivity

VPC2 private EC2 was accessed through the public EC2 bastion host using SSH ProxyCommand.

The private instance was successfully reached without assigning it a public IPv4 address.

4. Port 22 Verification

Connectivity to SSH port 22 was verified using:

timeout 5 bash -c '</dev/tcp/10.1.1.202/22' && echo "PORT 22 OPEN" || echo "PORT 22 CLOSED"

Result:

PORT 22 OPEN

---

🚨 Controlled Failure Test

A controlled failure test was performed by temporarily removing the required ICMP rule from the security group.

The peering connection and route tables remained unchanged.

Before

VPC Peering       → Active
Routes            → Correct
ICMP Rule         → Allowed
Ping              → Successful

After removing ICMP rule

VPC Peering       → Active
Routes            → Correct
ICMP Rule         → Blocked
Ping              → Failed

The ICMP rule was then restored and connectivity successfully recovered.

This demonstrates that routing establishes the network path, while security groups control whether the traffic is permitted.

---

🛠️ Troubleshooting Performed

During implementation, several networking issues were identified and resolved:

- Incorrect route table association
- Incorrect security group CIDR rules
- ICMP traffic being blocked
- Private connectivity verification
- SSH access to private EC2 through the bastion host
- Verification of route table entries
- Verification of VPC peering status

The issues were resolved by systematically checking:

1. VPC CIDR ranges
2. Subnet associations
3. Route tables
4. VPC peering status
5. Security Group rules
6. EC2 private IP addresses
7. Connectivity using "ping" and SSH



📚 Key Concepts Demonstrated

- Amazon VPC
- CIDR addressing
- Public and private subnets
- Internet Gateway
- Route Tables
- VPC Peering
- Security Groups
- EC2
- Bastion Host
- Private IP communication
- SSH ProxyCommand
- ICMP testing
- Network troubleshooting
- Controlled failure testing

---

📂 Project Structure

VPC-Nexus/
│
├── README.md
│
├── Documentation/
│   └── VPC_Nexus_Project_Documentation.docx
│
├── Architecture/
│   └── VPC_Nexus_Architecture.png
│
├── Screenshots/
│   ├── 01-VPCs.png
│   ├── 02-Subnets.png
│   ├── 03-Route-Tables.png
│   ├── 04-VPC-Peering.png
│   ├── 05-EC2-Instances.png
│   ├── 06-Forward-Ping.png
│   ├── 07-Reverse-Ping.png
│   ├── 08-Private-SSH.png
│   ├── 09-Controlled-Failure.png
│   └── 10-Cleanup-Checklist.png
│
└── Configuration/
    └── Network-Configuration.txt

---

📖 Documentation

Detailed project documentation is available in:

"Documentation/VPC_Nexus_Project_Documentation.docx"

The documentation contains the project architecture, AWS resource configuration, connectivity tests, controlled failure demonstration, and cleanup checklist.

---

🎯 Learning Outcome

This project provided practical experience in designing, configuring, connecting, and troubleshooting AWS VPC networks.

The implementation demonstrates how VPC isolation, routing, VPC peering, security groups, and private EC2 communication work together to create controlled cloud network connectivity.

---

👩‍💻 Project Information

Project: VPC Nexus
Domain: AWS Cloud Networking
Region: "ap-southeast-2"
Course: AWS
Batch: 1
Student: SWETHA SRI MANAGALAGIRI