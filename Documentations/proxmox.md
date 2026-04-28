# Homelab Documentation: Proxmox Configuration

###  1. Overview 

Proxmox serves as a hypervisor where we can run multiple VMs or containers. This lab runs
pfSense for firewall/routing, Windows Server for Active Directory and DHCP, TrueNAS
Scale for file serving, and a Zabbix LXC container for monitoring tool.

### 2. Network Config
The host network is designed to separate management traffic from virtualized service traffic.

|NAME|TYPE|VLAN AWARE|PORT/SLAVES|PURPOSE|
|----|----|----------|-----------|-------|
|nic0|Network Device|No|-|Additional NIC for pfSense LAN port|
|nic1|Network Device|No|-|WAN connection for Proxmox host|
|vmbr0|Bridge|No|nic1|Bridging the IP address of the Proxmox - 192.168.1.2/24 |
|vmbr1|Bridge| Yes|nic0| Trunk Port vlan aware 10,20,30,99|

### 3. /etc/network/interfaces Configuration

```bash
# Proxmox Host Network Configuration

auto vmbr0
iface vmbr0 inet static

address 192.168.1.2/24
gateway 192.168.1.1
bridge-ports nic1
bridge-stp off
bridge-fd 0

# vmbr1: VLAN Trunk for Services (VLANs 10, 20, 30, 99)

auto vmbr1
iface vmbr1 inet manual

bridge-ports nic0
bridge-stp off
bridge-fd 0
bridge-vlan-aware yes
bridge-vids 10 20 30 99
```

![](/img/proxmox1.png)

![](/img/proxmox2.png)

![](/img/proxmox3.png)