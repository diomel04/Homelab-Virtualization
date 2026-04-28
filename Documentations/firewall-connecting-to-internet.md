# Firewall pfSense

## Overview 

pfsense serve as a firewall on this network to pass,block,reject the outgoing or incoming traffic. 

## Connect to the Internet

After creating the internal network inside the pfSense 172.16.1.1 we can notice that the vlans on internal network still don't have an access to the internet. It happened because our VLANs gateways are on the switch and the routing between the pfsense and switch are not configure yet.

To fix this we need to configure the gateway, static routes and NAT outbound rules. 

1.  Add a ipv4 route on the switch `0.0.0.0 -> 172.16.1.1` 

![](/img/route.png)

2. On the pfSense, We need to make a gateway base on the ip address of the switch on the pfSense LAN. 

* in my case, my SG300 is 172.16.1.2 and configured as reservation on that address. 

![](/img/switchip.png)

![](/img/switchgateway.png)

3. Adding Static routes from pfSense, vlan network -> gateway we configured. 

![](/img/pfSensestaticroutes.png)

4. Creating a Firewall Aliases for the VLANs

![](/img/alias.png)

**note**: make sure the Source you select **Network** and Type the name of Alias on the Source Address.

5. Go to the Firewall Rules and create a new rule to pass traffic inside network to the internet.

![](/img/rule1.png)

![](/img/rule2.png)

**Note on Outbound NAT:**
By default, pfSense uses Automatic Outbound NAT Rule Generation. Once the static routes to the VLAN subnets are configured, pfSense will automatically create the necessary NAT rules to allow traffic from those subnets to exit via the WAN. No manual modification of Outbound NAT rules is required unless you are moving to a more restrictive or complex 'Manual' NAT configuration.

### Verification 

On my laptop that connected to the vlan 10, I ping 8.8.8.8 to check the connectity to the internet.

![](/img/internet%20ping.png)





