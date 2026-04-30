# Homelab Documentation: Zabbix Configuration

## Overview 

Zabbix is an open-source enterprise-grade network monitoring platform used to monitor the availability, performance, and health of IT infrastructure components including servers, virtual machines, network devices, and services.

In this homelab, Zabbix is deployed as an LXC container on Proxmox to provide centralized visibility across all network devices and systems within the environment.

### Deployment Details

| Component | Details |
|-----------|---------|
| **Zabbix Version** | 7.4 |
| **OS** | Ubuntu 24.04 LTS (Noble) |
| **Database** | MySQL |
| **Web Server** | Apache |
| **Deployment** | LXC Container on Proxmox |
| **IP Address** | 172.16.30.4 |
| **VLAN** | VLAN 30 |

### What Zabbix Monitors in This Homelab

| Device | Monitoring Method |
|--------|------------------|
| **pfSense** | SNMP / Zabbix Agent |
| **Windows Server** | Zabbix Agent |
| **TrueNAS** | SNMP |
| **Cisco SG300** | SNMP |
| **Proxmox** | Zabbix Agent / API |


## Installation

### LXC Container
First I created an LCX Container running Ubuntu 24.0 LTS ( Noble ) on the proxmox and set the ip address at 172.16.30.4/28 vlan 30 using vmnbr1 vlan aware

![](/img/zabbix1.png)

### Mysql installation 

```bash
sudo apt update
sudo apt install mysql-server

systemctl start mysql
```

### Installing zabbix 

for installing the zabbix you can follow the documentation [here](https://www.zabbix.com/download?zabbix=7.4&os_distribution=ubuntu&os_version=24.04&components=server_frontend_agent&db=mysql&ws=apache)

After installing from the console you would need to use browser to open the web ui **http://< host >/zabbix** example: http://172.16.30.4/zabbix

![alt text](/img/zabbix2.png)

After that you would proceed to the admin panel and put the default user and pass to log in.

username: Admin
password: zabbix

**note**: Case sensitive on username. 

![alt text](/img/zabbix3.png)

![](/img/zabbix10.png)

## Errors that I encounter during Installation

Issue: System Locale Fail on Zabbix Pre-requisites Check

During the Zabbix web installer pre-requisites check, System Locale shows Fail with the following error:

Cause: The Ubuntu LXC container was deployed without the en_US.UTF-8 locale generated, so Apache/PHP cannot find the required language files for the Zabbix frontend.

![](/img/zabbix4.png)
![](/img/zabbix5.png)

Resolution:

1. Install locales package

```bash
apt install locales -y
```

2. Generate en_US.UTF-8 locale

```bash
locale-gen en_US.UTF-8
```

3. Set it as system default

```bash
update-locale LANG=en_US.UTF-8
```

4. Restart Apache

```bash
systemctl restart apache2
```

5. Refresh the Zabbix web installer page

**Results**
System Locale changes from Fail to OK and all pre-requisites pass successfully.



## Adding VMs to the sabbix 

### Proxmox 

1. Create an API Tokens for sabbix. Go to Proxmox web ui -> Datacenter -> API Tokens and Add new token. After that you will receive your Secret Token. 

![](/img/apitoken.png)

**NOTE:** take note of the secret token after you click OK you will not see it again. 

2. Create an new host on zabbix. Go to Datacollection -> Host -> Create host. 

![](/img/hostt.png)

3. put your hostname, select the `Proxmox VE by HTTP` on the templates, `Virtual Machines` on the host group.

![](/img/hostt1.png)

4. Go to Macros > Inherited and host macros and find {$PVE.TOKEN.ID}, {$PVE.TOKEN.SECRET},{$PVE.URL.HOST}. Then click add. 

![](/img/hostt2.png)

**For Verification**

you can go to `Monitoring -> Host` you can see your Proxmox there and click graphs to check your real-time hardware usage. 

![](/img/hostt3.png)


### pfSense via Zabbix Agent

1.  Go to the `System -> Package Manager -> Available Package` and go install zabbix agent.

![](/img/install.png)

2. Go to the `Services -> Zabbix Agent` enable the service, add the server address of zabbix and the hostname:port of the zabbix.

![](/img/install2.png)

3. Go to the zabbix `Data Collection -> Host -> Create Host

![](/img/install3.png)

4. Input the Hostname, for the templates choose `FreeBSD by Zabbix agent` Add Interface `Agent` and Input the IP address of pfSense

![](/img/install4.png)

**note**: make sure the hostname is match on the pfSense and on the zabbix. 

### For the Verification

You can go to `Data Collection -> Host` you should see the greenbox on the column of pfSense host you created.

![](/img/install5.png)


### Truenas via SNMP

1. Go to TrueNas web ui `System -> Services` and turned on the `SNMP`.

![](/img/tn.png)

3. Go to the zabbix `Data Collection -> Host -> Create Host

![](/img/install3.png)

4. Input the Hostname, for the templates choose `TrueNAS CORE by SNMP` Add Interface `SNMP` and Input the IP address of TrueNAS.

![](/img/tn1.png)

### For the Verification

You can go to `Data Collection -> Host` you should see the greenbox on the column of Truenas host you created.

![](/img/tn2.png)

### Windows server via Zabbix Agent

1. Download and Install Zabbix agent [here](https://www.zabbix.com/download_agents?version=7.4&release=7.4.9&os=Windows&os_version=Server+2016+%2B&hardware=amd64&encryption=OpenSSL&packaging=MSI&show_legacy=0)

![](/img/zb.png)

2. Go to the zabbix `Data Collection -> Host -> Create Host

![](/img/install3.png)

3. Input the Hostname, for the templates choose `Windows by Zabbix agent` Add Interface `Agent` and Input the IP address of TrueNAS.

![](/img/zb2.png)

### For the Verification

You can go to `Data Collection -> Host` you should see the greenbox on the column of Windows host you created.

![](/img/zb3.png)

### SG300 Switch via SNMP

1. Open your web ui on Cisco Switch then Enable the SNMP `Security -> TCP/UDP Services`

![](/img/s1.png)

2. Go to `SNMP -> Communities` and add community string and the ip address of the zabbix server.

![](/img/s2.png)

**note**: make sure you dont share your community string name for your better privacy. 

2. Go to the zabbix `Data Collection -> Host -> Create Host

![](/img/install3.png)

3. Input the Hostname, for the templates choose `Network Generic Devices by SNMP` Add Interface `SNMP` and Input the IP address of Switch.

![](/img/s3.png)

### For the Verification

You can go to `Data Collection -> Host` you should see the greenbox on the column of Windows host you created.

![](/img/s4.png)
























