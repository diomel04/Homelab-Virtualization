# Homelab Documentation: Tailscale vpn to pfSense

## Overview 
Tailscale is a modern VPN solution built on top of WireGuard that simplifies secure remote access without the complexity of traditional VPN setups. Unlike conventional VPNs, Tailscale uses a mesh networking model where each device connects directly to each other (peer-to-peer) rather than routing all traffic through a central server.

In this homelab, Tailscale is installed on pfSense to act as a subnet router, allowing remote devices to securely access internal VLAN resources without needing the Tailscale client installed on every device.

Key features used: 

* Subnet Routing — exposes local VLANs (172.16.0.0/16) to remote Tailscale clients
* Remote Access — access homelab resources from anywhere without port forwarding
* Zero open inbound ports — no firewall port forwarding required on pfSense


## Installation of Tailscale on pfsense. 


1. Download the Package on the pfSense Go to `System -> Package Manager -> Available Packages` Search for Tailscale and download it

![](/img/tailscale5.png)

2. After Downloading you can now see the TailScale on the VPN Section.

![](/img/tailscale4.png)

3. Log in on the Admin Console of tailscale Go to `VPN -> Tailscale -> Status` Copy the link then paste it on the browser to Log in. 

![](/img/tailscale3.png)

### Verify the Status 

* Go to `VPN -> Tailscale` you should see the vpn is now online 

![](/img/tailscale2.png)

* you can also check on the tailscale admin if you are connected. 

![](/img/tailscale1.png)


## Add Routes for VPN Clients -> Internal Network

Go to `VPN -> Tailscale -> Settings` navigate to `Routing` and add your networks 

![](/img/tailscale6.png)

## Approving Subnets 

Go to your `tailscale admin panel at the browser` click the `3 dots` -> Edit route settings and select what you want to approve.

![](/img/tailscale7.png)

![](/img/tailscale8.png)


## ACLs for VPN network Clients


