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




