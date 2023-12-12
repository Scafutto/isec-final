# Network Security
For this scenario, let's say a company provides internet access to clients while inside their business. 

# Red Team
In this section we will use the **netdiscover** tool to get more information on the devices on the same network. An attacker could use this for several reasons, such as understanding the network topology, but also looking for vulnerable devices (especially IoT devices).


First, let's get the interface name and the network address with `ip a`:

```
──(kali㉿kali)-[~]
└─$ ip a

hidden output...

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:21:b1:d0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.0.10/24 brd 192.168.0.255 scope global dynamic noprefixroute eth0

hidden output...
```

Now, run the command `sudo netdiscover -i eth0 -r 192.168.0.0/24`. Change the values to match your scenario.

```
 Currently scanning: Finished!   |   Screen View: Unique Hosts              
                                                                    
 8 Captured ARP Req/Rep packets, from 5 hosts.   Total size: 480            
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.0.1     c0:c5:22:4c:6b:c7      4     240  ARRIS Group, Inc.        
 192.168.0.5     f4:7b:09:94:ba:79      1      60  Intel Corporate          
 192.168.0.11    08:00:27:1e:6b:cc      1      60  PCS Systemtechnik GmbH   
 192.168.0.6     2c:fd:a1:bc:83:57      1      60  ASUSTek COMPUTER INC.    
 192.168.0.252   00:00:ca:01:02:03      1      60  ARRIS Group, Inc.   
```


# Blue Team
A good solution for this problem would be separating the internal LAN for the one accessible by clients. This could be achieved by having different devices for each, or subnetting.

For this demonstration, we isolated the customer's network in a different LAN, let's try running the same commands again.

```
──(kali㉿kali)-[~]
└─$ ip a        

hidden output...

2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:21:b1:d0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.105/24 brd 192.168.1.255 scope global dynamic noprefixroute eth0

hidden output...
```

Now, running the new scan with `sudo netdiscover -i eth0 -r 192.168.1.0/24`, the results are:

```
 Currently scanning: Finished!   |   Screen View: Unique Hosts                                     

 2 Captured ARP Req/Rep packets, from 2 hosts.   Total size: 120                                   
 _____________________________________________________________________________
    IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.1.1     08:00:27:0d:ed:04      1      60  PCS Systemtechnik GmbH                          
 192.168.1.104   08:00:27:1e:6b:cc      1      60  PCS Systemtechnik GmbH    
```

By having a dedicated LAN for customers, this company could avoid an attacker knowing all their connected devices.