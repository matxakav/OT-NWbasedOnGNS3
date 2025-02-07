Distribution switch 3
```py
hostname vEOS-Dis-III
ip name-server vrf default 172.16.50.1
!
spanning-tree mode mstp
!
vlan 10,20
!
interface Ethernet1
   description link to IOSvL2-C1
   no switchport
   ip address 10.0.0.25/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034


interface Ethernet2
   description link to IOSvL2-C2
   no switchport
   ip address 10.0.0.29/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==
!
interface Ethernet3
   no switchport
   ip address 10.0.0.33/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==

interface Ethernet4
   description link to veosd2
   switchport trunk allowed vlan 10,20
   switchport mode trunk
!
interface Ethernet5
   description link to D2
   switchport trunk allowed vlan 30,40
   switchport mode trunk
interface Loopback0
   ip address 10.1.1.8/32
!
interface Management1
   description Link to SDN Gateway
   ip address 172.17.1.5/16
!
interface Vlan10
   ip address 192.168.10.252/24
   vrrp 10 peer authentication ietf-md5 key-string 7 yqR5KJBqI+v/QzlmfBfPZQ==
   vrrp 10 ipv4 192.168.10.254
!
interface Vlan20
   ip address 192.168.20.252/24
   vrrp 20 peer authentication ietf-md5 key-string 7 FBqWt0HgqJrI16piy59Scw==
   vrrp 20 ipv4 192.168.20.254
!
ip routing
!
ip route 0.0.0.0/0 10.0.0.10
!
router ospf 1
   router-id 10.1.1.8
   no passive-interface Ethernet3
   no passive-interface Ethernet4
   no passive-interface Ethernet5
   passive-interface Vlan10
   passive-interface Vlan20
   network 10.0.0.24/30 area 0.0.0.0
   network 10.0.0.28/30 area 0.0.0.0
   network 10.0.0.32/30 area 0.0.0.0
   network 10.1.1.8/32 area 0.0.0.0
   network 192.168.10.0/24 area 0.0.0.0
   network 192.168.20.0/24 area 0.0.0.0
   max-lsa 12000
```

Dist 2
```
hostname vEOS-Dis-II
ip name-server vrf default 172.16.50.1
!
spanning-tree mode mstp
!
vlan 10,20,30,40
!
management api netconf
   transport ssh admin
!
interface Ethernet1
   description Link to vIOS-Core-I
   no switchport
   ip address 10.0.0.17/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 bS8fz/oIV8WdkUoexvixrA==


interface Ethernet2
   description Link to vIOS-Core-II
   no switchport
   ip address 10.0.0.13/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==
!
interface Ethernet3
   description Link to vEOS-Dis-I
   no switchport
   ip address 10.0.0.2/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==
!
interface Ethernet4
   description Link to OpenSwitch-Acc-II
   switchport trunk allowed vlan 30,40
   switchport mode trunk
!
interface Ethernet5
   description Link to OpenSwitch-Acc-I
   switchport trunk allowed vlan 10,20
   switchport mode trunk


interface Ethernet6
   description link to d3
   no switchport
   ip address 10.0.0.34/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 4KdnNV75t8SAuFVAp+cuVg==


!
interface Management1
   description Link to SDN Gateway
   ip address 172.17.1.2/16
!
interface Vlan30
   ip address 192.168.30.252/24
   vrrp 30 priority-level 150
   vrrp 30 peer authentication ietf-md5 key-string 7 FBqWt0HgqJrI16piy59Scw==
   vrrp 30 ipv4 192.168.30.254
!
interface Vlan40
   ip address 192.168.40.252/24
   vrrp 40 priority-level 150
   vrrp 40 peer authentication ietf-md5 key-string 7 puOyeqkMgX3Ql1U7YaAQGg==
   vrrp 40 ipv4 192.168.40.254
!
ip routing
!
ip route 0.0.0.0/0 10.0.0.14
!
router ospf 1
   router-id 10.1.1.7
   passive-interface Ethernet4
   passive-interface Ethernet5
   no passive-interface Ethernet6
   passive-interface Vlan10
   passive-interface Vlan20
   passive-interface Vlan30
   passive-interface Vlan40
   network 10.0.0.0/30 area 0.0.0.0
   network 10.0.0.12/30 area 0.0.0.0
   network 10.0.0.16/30 area 0.0.0.0
   network 10.0.0.32/30 area 0.0.0.0
   network 10.1.1.7/32 area 0.0.0.0
   network 10.1.1.12/30 area 0.0.0.0
   network 192.168.30.0/24 area 0.0.0.0
   network 192.168.40.0/24 area 0.0.0.0
   max-lsa 12000
!
management ssh
   idle-timeout 300
   authentication mode password
!
end


```

Distribution switch 1

```
hostname vEOS-Dis-I
ip name-server vrf default 172.16.50.1
!
spanning-tree mode mstp
!
vlan 10,20,30,40
!
management api netconf
   transport ssh admin
!
interface Ethernet1
   description Link to vIOS-Core-II
   no switchport
   ip address 10.0.0.21/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 bS8fz/oIV8WdkUoexvixrA==
!
interface Ethernet2
   description Link to vIOS-Core-I
   no switchport
   ip address 10.0.0.9/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==
!
interface Ethernet3
   description Link to vEOS-Dis-II
   no switchport
   ip address 10.0.0.1/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 7 n+Pkjf+gomK+msssg0WWXA==
!
interface Ethernet4
   description Link to OpenSwitch-Acc-I
   switchport trunk allowed vlan 10,20
   switchport mode trunk
!

interface Ethernet5
   description Link to OpenSwitch-Acc-II
   switchport trunk allowed vlan 30,40
   switchport mode trunk
!


interface Loopback0
   ip address 10.1.1.6/32
!
interface Management1
   description Link to SDN Gateway
   ip address 172.17.1.1/16
!
interface Vlan10
   ip address 192.168.10.253/24
   vrrp 10 priority-level 150
   vrrp 10 peer authentication ietf-md5 key-string 7 yqR5KJBqI+v/QzlmfBfPZQ==
   vrrp 10 ipv4 192.168.10.254
!
interface Vlan20
   ip address 192.168.20.253/24
   vrrp 20 priority-level 150
   vrrp 20 peer authentication ietf-md5 key-string 7 FBqWt0HgqJrI16piy59Scw==
   vrrp 20 ipv4 192.168.20.254
!
interface Vlan30
   ip address 192.168.30.253/24
   vrrp 30 peer authentication ietf-md5 key-string 7 FBqWt0HgqJrI16piy59Scw==
   vrrp 30 ipv4 192.168.30.254
!
interface Vlan40
   ip address 192.168.40.253/24
   vrrp 40 peer authentication ietf-md5 key-string 7 puOyeqkMgX3Ql1U7YaAQGg==
   vrrp 40 ipv4 192.168.40.254
!
ip routing
!
ip route 0.0.0.0/0 10.0.0.10
!

router ospf 1
   router-id 10.1.1.6
   passive-interface Ethernet4
   passive-interface Ethernet5
   passive-interface Ethernet6
   passive-interface Vlan10
   passive-interface Vlan20
   passive-interface Vlan30
   passive-interface Vlan40
   network 10.0.0.0/30 area 0.0.0.0
   network 10.0.0.8/30 area 0.0.0.0
   network 10.0.0.20/30 area 0.0.0.0
   network 10.1.1.6/32 area 0.0.0.0
   network 10.1.1.8/30 area 0.0.0.0
   network 192.168.10.0/24 area 0.0.0.0
   network 192.168.20.0/24 area 0.0.0.0
   network 192.168.30.0/24 area 0.0.0.0
   network 192.168.40.0/24 area 0.0.0.0
   max-lsa 12000
!
management ssh
   idle-timeout 300
   authentication mode password
!


```
