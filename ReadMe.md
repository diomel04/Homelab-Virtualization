# Homelab Overview

A self-hosted environment built to simulate enterprise infrastructure for hands-on learning and skill development. 


## Features

* Segmented network using VLANs for isolation and security
* Inter-VLAN routing via Layer 3 switching (Cisco SG300)
* DHCP relay configured using IP Helper Address on the switch
* Firewall enforcement with pfSense (default-deny policy)
* Access control using ACLs to restrict traffic between VLANs
* Centralized authentication and management via Active Directory
* Automated IP address management using DHCP (Windows Server)
* Secure file storage with RAID 1 redundancy (TrueNAS)
* AD-integrated file sharing with user-based permissions
* VPN capability for secure remote access (pfSense, Tailscale)
* Real-time network and system monitoring using Zabbix
* Basic network troubleshooting setup (ping, tracert, IP conflict resolution)
* Scalable design to simulate enterprise network environments

## Devices and VM Inventory

### Physical Hardware
|Device|Model|Role|
|---|-----|-----|
|Proxmox Host| Intel i5-8350U, 16GB Ram, 2x 500 HDD| Hypervisor|
|Switch| Cisco SG300-20|Inter-Vlan, IP Helper, ACLs|
|Client Pc| Laptop Thinkpad T480 | For Testing DHCP on Switch Ports|


### Virtual Machines

|VM Name|OS|Role|vCPU|RAM|DISK|
|-----|---|-----|---|---|---|
|pfSense|pfSense|Firewall/Router,VPN|1|2GB|50GB|
|WinServer|Windows Server 2022|AD,DHCP|2|8GB|100G|
|File-Server|TrueNas Scale|File-Server RAID 1 AD Integrated|2|4GB|500GB 
|Zabbix|Zabbix|Network Monitoring|1|2GB|35GB|w

## NETWORK DIAGRAM

![](/Projects-and-HomeLabs/Home-Labs/Home-Lab-Infrastructure/img/netdiagram.png)

## VLAN DESIGN & IP ADDRESSING

|VLAN NAME|VLAN ID|NETWORK ADDRESS|CIDR|SUBNET MASK|PURPOSE|
|-|-|-|-|-|-|
|Trusted Network 1|10|172.168.8.1|/22|255.255.252.0|Workstation Segment 1|
|Trusted Network 2|20|172.16.20.1|/22|255.255.252.0|Workstation Segment 2|
|Servers|30|172.16.30.1|/28|255.255.255.240| Servers AD,DHCP,DNS & File Server
|Management|99|172.16.99.1|/29|255.255.255.248|Admin Access, Management|

