# TrueNas Scale File Server integrated with Active Directory


## Overview 

The TrueNAS Scale file server is integrated with Active Directory (AD) running on Windows Server 2022, allowing domain users and groups to authenticate and access shared network storage using their AD credentials. TrueNAS is joined to the domain and configured with SMB shares, enabling centralized permission management through AD security groups. This setup simulates a real-world enterprise file server where user access is controlled by the domain controller rather than local accounts — ensuring consistent access policies across the network.

### Creating Users and Groups on AD 

![](/img/userad.png)

![](/img/groups.png)

**NOTE**: Add a user for the TrueNas credential for the integration to AD.

### Verification AD Connectivity 

On my Laptop that connected to the vlan 20 on the switch I created a bridge on ethernet port and create an Windows VM via virtualbox to connect to the AD.

![](/img/adtest.png)

![](/img/adtest1.png)



## TrueNAS File Server

TrueNas will be the one who will manage our Files servers but the permission will be configured based on the groups or users we have on active directory.

![](/img/TrueNas.png)

as you can see on the image we have 3 hardisk 

* for Operating system
* Files Storage
* Backup RAID 1 

### Configuring Interfaces

I configure the IP Address via static at 172.16.30.3/28 
connected to vlan 30 via vmbr1 of the proxmox

![](/img/truenasip.png)

### Configuring Static Route/Gateway

Since we're on the vlan 30, we just need to put our gateway address of 172.16.30.1 to any destination ( 0.0.0.0/0 )

![](/img/truenasstaticroute.png)


### Verify the connectivity to other network via shell 

![](/img/truenasver.png)


## Integrating TrueNAS to Windows Server AD

1. You need to create an User on your AD dedicated for the TrueNAS Server.

2. Go to the TrueNAS web ui base on what is the ip address of the TrueNAS.

![](/img/trruenas.png)


3. Go to Systems > Network > Global Configuration Settings > Add the Ip address of Windows Server on DNS. 

![](/img/truenasdns.png)

4. Go to Credentials > Directory Services > Configure Directory Service. 

Enable the Service and select **Active Directory** on the configuration type

![](/img/add1.png)

![](/img/add2.png)


### If you ever got an problem connecting to AD 

#### Check the DNS 

Go to Windows Server > DNS MANAGER > Forward Lookup Zones & Reverse Lookup Zones

usually on Forward Lookup Zones its on automatic but on Reverse we need to configure it by adding New Zone for our homelab.local dns 

Right Click the Reverse Lookup Zone then Add New Zone and make it Primary and put your Network Address. 

![](/img/reverse1.png)

After Creating a New Zone Add a Pointer ( PTR ) inside the New zone you created.

![](/img/pointer.png)

Add your Host IP address and the name of your host domain name

![](/img/pointer1.png)

### Verification

After that Try again your connection on TRUENAS. You should be able to verify inside the credentials if you are connected to the AD.

![](/img/addd.png)

#### OR 

You can check on your Windows AD > Computer > you should see your TRUENAS there. 

![](/img/verad.png)


## Creating Datasets for File Sharing Raid 1 ( mirror )

on the Data on Creation on Dataset choose the **Mirror** on the layout 

![](/img/mirror.png)

Better to do it Manual Disk Selection so you can check what disk are you using on Mirroring 

![](/img/mirror1.png)

on my case I only have two so thats the reason why I can only see two disk drives. Then Proceed at the bottom to create a storage file.

**Error Encounter**

![](/img/error.png)


Error happens because on Proxmox virtual disk. TrueNAS is rejecting the RAID mirror because all virtual disks have no serial numbers. 

Solution:

go to Proxmox Shell

```bash
command syntax:

qm set <VMID> --<diskname> <storage>:vm-<VMID>-<disk-image>,serial=<serialnumber>

Example

qm set 102 --scsi1 local-lvm:vm-102-disk-1,serial=DISK001
qm set 102 --scsi2 local-lvm:vm-102-disk-2,serial=DISK002

Verify if it works

qm config 102

```

you can check on the PROXMOX > TRUENAS > HARDWARE for more details

![](/img/error%202.png)


![](/img/error3.png)

![](/img/error4.png)


After that reboot the TrueNAS VM and try to Create an Dataset again with RAID 1 Mirror.


## Shared Folders and permissions using AD 

![](/img/dataset.png)

![](/img/dataset1.png)

On the picture we can now use the Groups and Users on our AD inside the TRUENAS for file permissions. 

Example

![](/img/file.png)

![](/img/file1.png)

![](/img/file2.png)

on the picture we can see Kurth only belong to the Accounting Group not on Operations Group

And on the TrueNAS the HOMELAB\accounting only have an access on Accounting_Folder not on the Operations_Folder.

So the expected results on the Kurth PC he should have an access on the Accounting-Folder and when he try to open the Operations-Folder it should be rejected. 

![](/img/test1.png)

![](/img/test2.png)





