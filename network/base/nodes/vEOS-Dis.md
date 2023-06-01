# vEOS-Dis-I & II Configuration

![image](https://user-images.githubusercontent.com/69375071/210246963-05925b98-edc1-48ee-b06d-87167bb5f29c.png)

## vEOS-Dis-I

vEOS is an Arista product subject to Cisco's configuration convention. The configuration process is similar to `OpenSwitch-Acc-I`, with differences addressed below.

- Login with username `admin`, no password.
- Upon the first start, the vEOS node will try to automatically configure itself using the "zerotouch" feature. That is useful when the node is connected to some DHCP servers, but we don't need it. We should use `zerotouch disable` to permanently disable this feature. After that, the node will reboot and then you can start manually configure it.
  - You only need to run "`zerotouch disable`" once for the first time when you start a brand new vEOS node. You will never need it after that.

```
zerotouch disable
```

- In vEOS nodes, we can copy running-config to startup-config using a shorter command: `wr`. This command is commonly adopted in modern Cisco appliances to save your configuration (our OpenSwitch is a bit out of date, however).

```
wr
```

Use the following configuration for `vEOS-Dis-I`. Note that we already append `wr`.

- Make sure to use `en` and `conf t` to enter the "config mode". I won't remind you again of it.

```
hostname vEOS-Dis-I
ip name-server 8.8.8.8
!
vlan 10,20,30,40
!
interface Ethernet1
   description Link to vIOS-Core-II
   no switchport
   ip address 10.0.0.21/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
!
interface Ethernet2
   description Link to vIOS-Core-I
   no switchport
   ip address 10.0.0.9/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
!
interface Ethernet3
   description Link to vEOS-Dis-II
   no switchport
   ip address 10.0.0.1/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
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
interface Ethernet6
   description Link to Management OpenSwitch-Acc-I
   no switchport
   ip address 10.1.1.10/30
!
interface Ethernet7
   shutdown
!
interface Loopback0
   ip address 10.1.1.6/32
!
interface Management1
   shutdown
!
interface Vlan10
   ip address 192.168.10.253/24
   vrrp 10 peer authentication ietf-md5 key-string MiKei10!
   vrrp 10 ipv4 192.168.10.254
!
interface Vlan20
   ip address 192.168.20.253/24
   vrrp 20 peer authentication ietf-md5 key-string MiKei10!
   vrrp 20 ipv4 192.168.20.254
!
interface Vlan30
   ip address 192.168.30.253/24
   vrrp 30 priority-level 150
   vrrp 30 peer authentication ietf-md5 key-string MiKei10!
   vrrp 30 ipv4 192.168.30.254
!
interface Vlan40
   ip address 192.168.40.253/24
   vrrp 40 priority-level 150
   vrrp 40 peer authentication ietf-md5 key-string MiKei10!
   vrrp 40 ipv4 192.168.40.254
!
ip routing
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
!
end
!
wr
!
```

## vEOS-Dis-II

Use the following configuration for `vEOS-Dis-II`.

```
hostname vEOS-Dis-II
ip name-server 8.8.8.8
!
vlan 10,20,30,40
!
interface Ethernet1
   description Link to vIOS-Core-I
   no switchport
   ip address 10.0.0.17/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
!
interface Ethernet2
   description Link to vIOS-Core-II
   no switchport
   ip address 10.0.0.13/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
!
interface Ethernet3
   description Link to vEOS-Dis-I
   no switchport
   ip address 10.0.0.2/30
   ip ospf network point-to-point
   ip ospf authentication message-digest
   ip ospf message-digest-key 1 md5 #MyPass!034
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
!
interface Ethernet6
   description Link to Management OpenSwitch-Acc-II
   no switchport
   ip address 10.1.1.14/30
!
interface Ethernet7
   shutdown
!
interface Loopback0
   ip address 10.1.1.7/32
!
interface Management1
   shutdown
!
interface Vlan10
   ip address 192.168.10.252/24
   vrrp 10 peer authentication ietf-md5 key-string MiKei10!
   vrrp 10 ipv4 192.168.10.254
!
interface Vlan20
   ip address 192.168.20.252/24
   vrrp 20 peer authentication ietf-md5 key-string MiKei10!
   vrrp 20 ipv4 192.168.20.254
!
interface Vlan30
   ip address 192.168.30.252/24
   vrrp 30 priority-level 150
   vrrp 30 peer authentication ietf-md5 key-string MiKei10!
   vrrp 30 ipv4 192.168.30.254
!
interface Vlan40
   ip address 192.168.40.252/24
   vrrp 40 priority-level 150
   vrrp 40 peer authentication ietf-md5 key-string MiKei10!
   vrrp 40 ipv4 192.168.40.254
!
ip routing
!
router ospf 1
   router-id 10.1.1.7
   passive-interface Ethernet4
   passive-interface Ethernet5
   passive-interface Ethernet6
   passive-interface Vlan10
   passive-interface Vlan20
   passive-interface Vlan30
   passive-interface Vlan40
   network 10.0.0.0/30 area 0.0.0.0
   network 10.0.0.12/30 area 0.0.0.0
   network 10.0.0.16/30 area 0.0.0.0
   network 10.1.1.7/32 area 0.0.0.0
   network 10.1.1.12/30 area 0.0.0.0
   network 192.168.10.0/24 area 0.0.0.0
   network 192.168.20.0/24 area 0.0.0.0
   network 192.168.30.0/24 area 0.0.0.0
   network 192.168.40.0/24 area 0.0.0.0
!
end
!
wr
!
```
