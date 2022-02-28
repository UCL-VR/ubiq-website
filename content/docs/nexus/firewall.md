---
title: "Firewall"
description: ""
lead: ""
date: 2022-01-18T19:58:14+01:00
lastmod: 2022-01-18T19:58:14+01:00
draft: true
images: []
toc: false
weight: 40
---

Be aware when creating your own clones that you will need to open your chosen ports on the local firewall and reload it.

To see the firewall rules on CentOS, give the command,

{{< highlight go >}}  sudo iptables -L -n -v --line-n {{< /highlight >}}

The -n argument shows the port numbers, rather than showing the names of typical services that run on them. -v shows the interface, and --line-n shows line numbers, which will be important when adding rules.

To add a new rule give the commands,

{{< highlight go >}}sudo iptables -I INPUT 5 -p tcp --dport 8004 -j ACCEPT
sudo service iptables save {{< /highlight >}}

The number after INPUT indicates the line that the rule should be added at. The ACCEPT rule must come before the catch-all REJECT rule. For example, the REJECT rule is on line 11 below and will reject all packets not matching any of the rules above it.

{{< highlight go >}}  
1    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0            state RELATED,ESTABLISHED
2    ACCEPT     icmp --  0.0.0.0/0            0.0.0.0/0
3    ACCEPT     all  --  0.0.0.0/0            0.0.0.0/0
4    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            state NEW tcp dpt:22
5    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8006
6    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8005
7    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8004
8    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8003
9    ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8002
10   ACCEPT     tcp  --  0.0.0.0/0            0.0.0.0/0            tcp dpt:8001
11   REJECT     all  --  0.0.0.0/0            0.0.0.0/0            reject-with icmp-host-prohibited


 {{< /highlight >}}



The iptables save is for the iptables-service, which is installed on Nexus, and allows saving and automatically reloading firewall rules on reboot.

Be aware also that opening the ports locally will not make them available on the public internet, but only via the CS VPN. Nexus has range 8000-8020 open on the CS and ISD firewalls.