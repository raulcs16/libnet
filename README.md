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

### Computers, Servers and Nodes
