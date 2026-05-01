# Lib Net Project

## Table of Contents

- [Lib Net Project](#lib-net-project)
  - [Table of Contents](#table-of-contents)
  - [Overview](#overview)
    - [Project Abstract](#project-abstract)
    - [Technical Requirements](#technical-requirements)
    - [The Problem](#the-problem)
    - [Phases of Deployment](#phases-of-deployment)
  - [Design](#design)
    - [Domain, Groups and Security Groups](#domain-groups-and-security-groups)
    - [Campus LAN Design](#campus-lan-design)
    - [Computers, Servers and Nodes](#computers-servers-and-nodes)
  - [Phase 0: Bringing up the Network](#phase-0-bringing-up-the-network)
  - [Phase 1](#phase-1)
  - [Phase 2](#phase-2)
  - [Phase 3](#phase-3)
  - [Phase 4](#phase-4)
  - [Phase 5](#phase-5)

## Overview

### Project Abstract

The core focus on this project is to outline and provision a segmented network for a public library

### Technical Requirements

- PC equipped with Windows 11 Pro, 32 GB of ram, 2 TB harddrive, stable ethernet connection
- GNS3 enviorement
- vm running windows server 2025 standard core, 2GB ram, 100 GB storage
- Cisco IOU-L3
- Cisco IOU-L2 image devices
- vm ubuntu pc acting as an ansible node server

### The Problem

**Vulnerability in Multi-Tenant Public Infrastructure:**

Public libraries serve as high-traffic digital hubs where the demand for unrestricted information access conflicts directly with the necessity of institutional data security. The primary challenge lies in the convergence of two disparate user bases on a single physical infrastructure:

**Staff & Administrative Operations:**

These entities require access to sensitive internal databases, Active Directory Domain Controllers (AD DC), and Integrated Library Systems (ILS) containing patron records and financial data

**Public Access Patrons:**

This group requires high-bandwidth internet access but presents a high risk for malware introduction, network sniffing, and unauthorized lateral movement.

Without rigorous logical segmentation, a single compromised public terminal could serve as an entry point for a lateral pivot attack against the library’s administrative core. Furthermore, the lack of Quality of Service (QoS) and traffic policing results in public media consumption degrading the performance of critical staff operations. This project addresses the "Flat Network" fallacy by implementing a hardened, multi-tier architecture.

### Phases of Deployment

**Phase 0 Bringing Up the Network:**

For the first phase, our goal is to simply bring up the network topology such that we are able to establish WAN connectivity.

**Phase I Identity And Access Management:**

Provisioning the Windows Server 2025 standard core instance, establishing domain forests, and defining security groups that will govern the network access

**Phase II Layer 2 Hardening:**

Implementing VLANs on Cisco IOU-L2 devices to isolate traffic at the broadcast level. Configuration of port security to mitigate unauthorized hardware insertion

**Phase III Logical Gateway & DHCP Relay:**

Provision the windows server as to offer DNS and DHCP services, correctly configure layer 3 device to allow IP address forwarding.

**Phase IV Permiter Security & NAT:**

Implementing ACLs on the L3 router in order to enforce a "deny all" principle between public and staff segments while allowing outbound WAN traffic

**Phase V Final System Validation & Automation:**

Execution of stress test and connectivity audits to ensure compliance with the initial "Technical Requiements"

## Design

### Domain, Groups and Security Groups

The domain architecture utilizes a single domain forests model *lib.local* in order to centralize authentication. To enforce the principle of *Least Previlage*, the domain will contain the following functional **Organziation Units**:

- **IT**
- **Finance**
- **Librarians**

then in order to grant various levels of access within the domain we have the following **security groups**:

- **IT-SysAdmin**
- **IT-NetworkAdmin**
- **IT-Support**:
- **Finance-Director**:
- **Lib-Admin**:
- **Lib-Staff**:
- **Public-Guest**:

### Campus LAN Design

The LAN topology that best suits the needs of this virtual library is a campus lan design. A pair of redundant edge routers will serve as the gateway to the WAN provide by ISPs. Next connected these will be a pair of switches serving as the core and distribution (CD) layer in the topology. Finally from the CD switches there will be 4 access switches, where 1 of the access switches will serve as our Service and Management switch in order to provide administrators and services a path into the network.

| Subnet           | use case            |
|------------------|---------------------|
| 172.16.255.x/32  | Loopback (Managment)|
| 192.168.vlan.x/24| Campus Vlan        |

### Computers, Servers and Nodes

## Phase 0: Bringing up the Network

- Router Configuration:
- Steps 1: connect router to ips, interface should be configured via dhcp

```js ios r1 config
// get ip from isp
(config)# int e0/0
(config-if)# no shutdown
(config-if)# ip address dhcp
//>10.0.0.114 mask 255.255.255.0
(config)# int loopback 0
(config-if)# ip address [172.16.255.1] 255.255.255.255
(config-if)# no shutdown

//inteface connected to cd1
(config-if)# ip adddress [192.168.10.1] 255.255.255.0
(config-if)# no shutdown

//route to d1
(config)# ip route 192.168.0.0 255.255.0.0 [ip cd1= 192.168.10.11]
```

```js ios cd1 config
//enable layer 3 routing
(config)# ip routing
//interface facing r1
(config-if)# no switchport 
(config-if)# ip address [192.168.10.11] 255.255.255.0
(config-if)# no shutdown

(config)# ip route 0.0.0.0 0.0.0.0 [r1 ip= 192.168.10.1]

//loopback
(config)# int loopback 0
(config-if)# ip address [172.16.255.11] 255.255.255.255
(config-if)# no shutdown

```

- Step 2: PAT

```js ios  router configure
(config)# int [facing isp]
(config-if)# ip nat outside
(config-if)# int [facing cd switch]
(config-if)# ip nat inside
//acl to permit range of ip
(config)# access-list 1 permit 192.168.10.0 0.0.0.255
(config)# ip nat inside source list 1 interface e0/0 overload
```

- step 3: Access Switches Trunks and Access Ports

```js ios  trunk configuration
(config)# vlan 10
(config)# name management
(config)# int vlan 10 
(config)# ip address [192.168.10.1] 255.255.255.0
(config)# no shutdown
(config)# int [facing linked switch]
(config)# switchport
(config-if)# switchport trunk encapsulation dot1q
(config-if)# switchport mode trunk
```

```js ios  access port configuration
(config)# int range [interface for end hosts]
(config-if)# switchport mode access
(config-if)# swtichport access vlan 1 //for now
(config-if)# spanning-tree portfasnt
```

## Phase 1

## Phase 2

## Phase 3

## Phase 4

## Phase 5
