# Nexus NX-SDK Python Application for Tracking IP Movement Across Interfaces

**Authors**:
* Christopher Hart - chart2@cisco.com
* Yogesh Ramdoss - yramdoss@cisco.com

## Overview

This repository contains an NX-SDK application that tracks the movement of a user-specified IP address across interfaces on a Cisco Nexus. 

For example, let's assume that a Cisco Nexus 9000 is connected to two ESXi clusters; one off of Ethernet1/5, and the other off of Ethernet1/17. Let's assume that a virtual machine with an IP of 192.0.2.10 is constantly moving between these two ESXi clusters, causing its MAC address to move between Ethernet1/5 and Ethernet1/17. Given an input of 192.0.2.10, this NX-SDK application will report that the IP is moving between Ethernet1/5 and Ethernet1/17, as follows:

```
N9K# show ip-movement 192.0.2.10
192.0.2.10 is currently present in ARP table, MAC address 0050.56c7.2731
0050.56c7.2731 is currently present in MAC address table on interface Ethernet1/5, VLAN 1
0050.56c7.2731 has been moving between the following interfaces, from most recent to least recent:
        Fri Apr 20 12:05:17 2018 - Ethernet1/5 (Current interface)
        Fri Apr 20 12:04:13 2018 - Ethernet1/17
        Fri Apr 20 12:04:13 2018 - Ethernet1/5
        Fri Apr 20 12:03:50 2018 - Ethernet1/17
        Fri Apr 20 12:03:50 2018 - Ethernet1/5
        Fri Apr 20 12:03:26 2018 - Ethernet1/17
```

This application requires a minimum software release of NXOS 7.0(3)I7(1).

## Usage

The user may choose to either generate the necessary RPM themselves from the ip_move.py file, or utilize the pre-built RPMs. Copy the RPm to the device and install it as if it were a SMU:

```
switch# install add bootflash:ip-movement-1.0-1.0.0.x86_64.rpm
[####################] 100%
Install operation 32 completed successfully at Mon Jun 4 12:21:13 2018

switch# 2018 Jun 4 12:21:13 switch %PATCH-INSTALLER-3-PATCH_INSTALLER_GENERIC_LOG_MSG: Install operation 15 completed successfully at Mon Jun 4 12:21:13 2018

switch# install activate ip-movement-1.0-1.0.0.x86_64
[####################] 100%
Install operation 32 completed successfully at Mon Jun 4 12:21:44 2018

2018 Jun 4 15:51:44 switch %PATCH-INSTALLER-3-PATCH_INSTALLER_GENERIC_LOG_MSG: Install operation 32 completed successfully at Mon Jun 4 12:21:44 2018

switch#
```

Next, enable NX-SDK (if it is not enabled already) and start the application:

```
switch# conf t
Enter configuration commands, one per line. End with CNTL/Z.
switch(config)# feature nxsdk
switch(config)# nxsdk service-name ip-movement
```

Finally, verify that the application is installed and running correctly:

```
switch(config)# show nxsdk internal service

NXSDK Started/Temp unavailabe/Max services : 1/0/32
NXSDK Default App Path         : /isan/bin/nxsdk
NXSDK Supported Versions       : 1.0 1.5

Service-name              Base App        Started(PID)      Version    RPM Package
------------------------- --------------- ----------------- ---------- ------------
------------
ip-movement               nxsdk_app1      VSH(4901)         1.5        ip-movement-1.0-1.5.0.x86_64
```

Now you can run commands offered by the application:

```
switch# show ip-movement 192.0.2.10
192.0.2.10 is currently present in ARP table, MAC address 0050.56c7.2731
0050.56c7.2731 is currently present in MAC address table on interface Ethernet1/5, VLAN 1
0050.56c7.2731 has been moving between the following interfaces, from most recent to least recent:
        Fri Apr 20 12:05:17 2018 - Ethernet1/5 (Current interface)
        Fri Apr 20 12:04:13 2018 - Ethernet1/17
        Fri Apr 20 12:04:13 2018 - Ethernet1/5
        Fri Apr 20 12:03:50 2018 - Ethernet1/17
        Fri Apr 20 12:03:50 2018 - Ethernet1/5
        Fri Apr 20 12:03:26 2018 - Ethernet1/17
```
