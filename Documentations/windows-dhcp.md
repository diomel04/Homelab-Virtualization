# Homelab Documentation: Windows Server DHCP


## Overview 

Windows Server DHCP is a service used to automatically assign IP addresses and network configuration settings to devices within a network. It simplifies network management by eliminating the need for manual IP configuration, ensuring that clients receive valid IP addresses, subnet masks, default gateways, and DNS information from a centralized server. This improves efficiency, reduces configuration errors, and ensures consistent network connectivity across all connected devices.


### DHCP Scopes

|VLAN ID|NETWORK|SUBNET MASK|STARTING IP ADDRESS|END IP ADDRESS|GATEWAY|DNS|
|-------|-------|-------------------|--------------|-------|---|-|
|10|172.16.8.0|255.255.252.0|172.16.8.20|172.16.8.254|172.16.8.1|1.1.1.1|
|20|172.16.20.0|255.255.252.0|172.16.20.20|172.16.20.254|172.16.20.1|1.1.1.1|
|30|172.16.30.0|255.255.255.240|172.16.30.5|172.16.30.14|172.16.30.1|1.1.1.1|
|99|172.16.99.0|255.255.255.248|172.16.99.2.|172.16.99.6|172.16.99.1|1.1.1.1|

![](/img/pool.png)

### DHCP FLOW 

![](/img/dhcpflow.png)

### Verification 

you can use cmd and ipconfig /all command to check if your dhcp is enable and to check your ip address. and If its on 169.x.x.x somethings wrong with the flow, so double check the ip address you put on the IP helper on the L3 Switch or pfSense depends what you use. 

![](/img/ipconfig.png)

as we can see here on the picture

DHCP is Enabled: Yes <br>
DHCP Server: 172.16.30.2

and also a correct ip address inside the network of 172.16.20.x/22 because we're on the vlan 20 port access right now. 

#### you can also check on your DHCP server and go to leashes

you should see your ip address there and computer name. 

![](/img/ipaddser.png)

**NOTE:** Make sure you already configured the switch IP Helper.

























