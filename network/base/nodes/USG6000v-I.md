# USG6000v-I Configuration

HUAWEI's product is quite different from Cisco's.

- The `en` and `conf t` is replaced by `system-view`.
- The `exit` is replaced by `quit`.
- The `wr` is replaced by `save` and `yes`.

USG6000v has a 15-day trial licence, so you need to redeploy it every half month. Compared with unlicenced Cisco ASAv firewall, USG6000v with trial licence has higher bandwidth.

- USG6000v use `security-policy` instead of Access Control List (ACL). It is quite self-explanatory. However, the sequence of these policies matters. The USG6000v will match the traffic to security policies one by one, and stop at the first success. So, you should deploy narrow policy before loose ones to avoid loose policies permit the harmful traffic that should be denied in narrow policies. Chinese users refer to [this blog](https://zhuanlan.zhihu.com/p/395704563) for more information.

```
system-view
sysname USG6000v-I

info-center disable
undo web-manager enable
undo web-manager security enable

interface GigabitEthernet 0/0/0
undo ip address
undo ip binding vpn-instance default
shutdown

interface GigabitEthernet 1/0/0
description Link to vIOS-Core-II
alias INSIDE0
service-manage ping permit
service-manage http permit
service-manage https permit
ip address 172.16.0.13 30

interface GigabitEthernet 1/0/1
description Link to vIOS-Core-I
alias INSIDE1
service-manage ping permit
service-manage http permit
service-manage https permit
ip address 172.16.0.9 30

interface GigabitEthernet 1/0/2
description Link to vIOS-Edge-I
alias OUTSIDE
service-manage ping permit
service-manage http permit
service-manage https permit
ip address 172.16.0.1 30

interface GigabitEthernet 1/0/3
description Link1 to vIOS-Ser-I
alias SERVER0
service-manage ping permit
service-manage http permit
service-manage https permit
ip address 172.16.0.5 30

interface GigabitEthernet 1/0/4
description Link2 to vIOS-Ser-I
alias SERVER1
service-manage ping permit
service-manage http permit
service-manage https permit
ip address 172.16.0.17 30

interface GigabitEthernet1/0/5
shutdown

interface GigabitEthernet1/0/6
shutdown

ip route-static 0.0.0.0 0 172.16.0.2
ip route-static 192.168.0.0 24 NULL 0

firewall zone trust
add interface GigabitEthernet 1/0/0
add interface GigabitEthernet 1/0/1

firewall zone dmz
add interface GigabitEthernet 1/0/3
add interface GigabitEthernet 1/0/4

firewall zone untrust
add interface GigabitEthernet 1/0/2

ospf 1 router-id 10.1.1.3
default-route-advertise always
area 0
authentication-mode md5 1 #MyPass!034
network 172.16.0.4 255.255.255.252
network 172.16.0.8 255.255.255.252
network 172.16.0.12 255.255.255.252
network 172.16.0.16 255.255.255.252

security-policy

# enable end_vlans
rule name end_vlans
source-address 172.16.50.0 24
source-address 192.168.0.0 16
destination-address 172.16.50.0 24
destination-address 192.168.0.0 16
action permit

# enable in to dns/http/https
rule name dns_http_https
source-zone trust dmz
destination-zone untrust
service dns http https
action permit

# enable in to ping others
rule name ping
source-zone dmz local trust
service icmp
action permit

# save configuration
quit
quit
save
yes

```
