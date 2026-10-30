# AWS-EC2-Networking-Bastion-Host-VPC-Connectivity-Lab
# AWS EC2 Networking, Bastion Host & VPC Connectivity Lab

## 📌 Project Overview

This project focuses on understanding AWS networking fundamentals through hands-on implementation and troubleshooting of EC2 connectivity, Bastion host access, security groups, routing, and VPC peering concepts.

**Cloud Provider:** Amazon Web Services (AWS)  
**Region:** Asia Pacific (Mumbai) — `ap-south-1`

### Objectives

- Understand EC2 instance connectivity using SSH.
- Configure and validate secure Bastion host access.
- Explore private IP communication between EC2 instances.
- Understand VPC peering and route table configuration.
- Troubleshoot ICMP, TCP, and SSH connectivity.
- Learn how security groups, network ACLs, and route tables affect network traffic.

## 🏗️ Architecture

```text
                  Administrator
                Windows PowerShell
                        |
                    SSH : 22
                        |
                +----------------+
                |  Bastion Host  |
                | Public Subnet  |
                +----------------+
                        |
                  Private SSH
                        |
                +----------------+
                |     App A      |
                | 10.10.1.10*    |
                +----------------+
                        |
                  SSH / ICMP
                        |
                +----------------+
                |     App B      |
                | 10.10.2.10*    |
                +----------------+
```

> **Note:** The IP addresses are based on the lab notes and should be verified against the EC2 console. Confirm the actual VPC IDs, subnet CIDRs, and peering configuration before treating this diagram as the final deployed architecture.

## 🛠️ AWS Services and Technologies

- Amazon EC2
- Amazon VPC
- Public and private subnets
- Route tables
- VPC peering
- Security groups
- Network ACLs
- Linux command-line tools
- SSH
- ICMP and TCP connectivity testing
- Netcat (`nc`)

## 🚀 Implementation Phases

### Phase 1: EC2 Instance Setup

Three EC2 instances were used in the lab:

| Instance | Purpose |
|---|---|
| `ewb-bastion` | Administrative entry point |
| `ewb-app-a` | Private application instance |
| `ewb-app-b` | Application instance for connectivity testing |

SSH was used to access the Linux instances and inspect their network configuration.

**Learning outcome:** Understanding EC2 access and the distinction between public and private IP addresses.

### Phase 2: TCP Connectivity Testing

Netcat was selected to test TCP port connectivity independently of ICMP.

```bash
nc -zv -w 3 10.10.2.10 22
```

**Purpose:** Check whether the destination's SSH port is reachable over TCP.

The initial test encountered a missing-command issue. A final successful Netcat test result has not yet been recorded.

### Phase 3: VPC Peering and Route Validation

This phase focuses on understanding communication between VPCs using private IP addresses.

The following configuration items need to be verified:

- VPC IDs and IPv4 CIDR blocks
- VPC peering connection status
- Route table associations for the relevant subnets
- Routes to the destination VPC CIDR
- Security group rules and network ACLs

For communication across peered VPCs, the peering connection must be active, the relevant route tables must contain the required routes, and network security controls must permit the traffic.

**Status:** Final VPC topology and peering configuration require confirmation.

### Phase 4: Bastion Host Security

The Bastion host was used as the entry point for administrative access to private instances.

Reported results:

| Test | Result |
|---|---|
| SSH to Bastion | Successful |
| SSH from Bastion to App A | Successful |
| SSH test involving App B | Reported successful |
| App A public IPv4 address | None reported |

#### Security objectives

- Restrict Bastion SSH access to a trusted administrator IP.
- Permit private-instance SSH only from authorized sources.
- Avoid unnecessary public IP exposure on application instances.
- Use least-privilege security group rules.
- Keep private SSH keys secure.

> Successful SSH tests establish connectivity for the tested paths but do not independently verify that all security rules follow least-privilege principles.

### Phase 5: Network Troubleshooting

This phase explores how routing and network security controls affect communication between EC2 instances.

#### 5.1 ICMP Test

The following command was run from App A:

```bash
ping -c 4 10.10.2.10
```

**Observed result:** Network or destination unreachable.

This indicates that the destination was not reachable through the attempted network path. The root cause has not yet been established.

Potential areas to investigate:

- Destination IP address accuracy
- Local Linux routing table
- VPC and subnet route tables
- VPC peering configuration, if applicable
- Security groups and network ACLs

#### 5.2 Route Diagnostics

The following commands can help identify the route Linux would use to reach App B:

```bash
ip route
```

```bash
ip route get 10.10.2.10
```

These commands inspect the local routing table and selected route without changing network configuration.

**Status:** Diagnostic output is pending.

#### 5.3 Understanding Connectivity Results

| Protocol or tool | Purpose |
|---|---|
| ICMP (`ping`) | Tests ICMP echo reachability |
| TCP (`nc`) | Tests connectivity to a specific TCP port |
| SSH | Tests remote login using TCP port 22 and authentication |
| `ip route` | Displays the local routing table |
| `ip route get` | Shows the selected route to a destination |

A failed ping does not necessarily mean SSH is unavailable. Similarly, successful SSH does not prove that ICMP traffic is permitted.

## 🔐 Security Best Practices

- Restrict SSH access to trusted sources.
- Avoid exposing SSH to `0.0.0.0/0` unnecessarily.
- Avoid public IP addresses on private application instances unless required.
- Permit only the traffic needed for the intended communication paths.
- Validate route tables before modifying routes.
- Remember that security groups are stateful, while network ACLs are stateless.
- Avoid copying private SSH keys between instances unnecessarily.
- Consider AWS Systems Manager Session Manager for administrative access where appropriate.

## 📊 Project Status

| Component | Status |
|---|---|
| EC2 setup and SSH access | Performed |
| Bastion-to-App A connectivity | Successful |
| App B SSH connectivity | Reported successful |
| Bastion security review | Performed; final rule validation pending |
| VPC peering and routes | Configuration requires confirmation |
| ICMP connectivity | Network or destination unreachable |
| Route diagnostics | Pending |
| Final end-to-end validation | Pending |

## 📚 Key Learnings

- How EC2 instances communicate using private IP addresses.
- How a Bastion host provides controlled administrative access.
- How route tables determine where network traffic is sent.
- How VPC peering enables private communication between VPCs.
- How security groups and network ACLs affect traffic.
- Why ICMP, TCP, and SSH tests can produce different results.
- How Linux networking commands help troubleshoot connectivity.

## 📝 Conclusion

This hands-on project explores AWS networking fundamentals through EC2 connectivity testing, Bastion host access, route validation, and network troubleshooting.

The reported SSH tests demonstrated connectivity along the tested paths, while the unsuccessful ICMP test highlighted the importance of investigating routing and network configuration rather than assuming a single cause.

The remaining tasks are to verify the final VPC topology, capture routing diagnostics, validate the security rules, and complete end-to-end connectivity testing.

## 📖 References

- [Amazon VPC — How VPCs work](https://docs.aws.amazon.com/vpc/latest/userguide/how-it-works.html)
- [Amazon VPC — Route tables](https://docs.aws.amazon.com/vpc/latest/userguide/VPC_Route_Tables.html)
- [Amazon VPC — VPC peering](https://docs.aws.amazon.com/vpc/latest/peering/working-with-vpc-peering.html)
- [Amazon VPC — Infrastructure security](https://docs.aws.amazon.com/vpc/latest/userguide/infrastructure-security.html)

