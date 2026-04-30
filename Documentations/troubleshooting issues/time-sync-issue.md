# Homelab Documentation: TROUBLESHOOTING

**ISSUE**: Windows Server time on the VMs are not in sync.

![](/img/troubleshooting.png)

**CAUSE**: Windows Server Time and Date is not on sync.

**EFFECTS**: Windows Server system time is out of sync with the Zabbix server by more than 60 seconds, causing inaccurate timestamps on collected metrics and potentially affecting alert timing and data reliability.


**Resolution**

1. On the Domain Controller, run CMD as Administrator:

```bash
w32tm /config /manualpeerlist:"time.google.com,time.cloudflare.com" /syncfromflags:manual /reliable:YES /update
net stop w32tm
net start w32tm
w32tm /resync /force
```

**verification**: 
```bash
w32tm/query/status
```

**Purpose**: This command configures the Domain Controller to synchronize time from external NTP servers (Google and Cloudflare), restarts the Windows Time service to apply the changes, and forces an immediate time resync to ensure all domain-joined machines inherit the correct time automatically.

2. Go to `Settings -> Date and Time` fix the time zone and sync. 


Results: 

![](/img/results.png)
![](/img/results1.png)
