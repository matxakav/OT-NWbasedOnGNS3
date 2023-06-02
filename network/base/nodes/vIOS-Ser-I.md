# vIOS-Ser-I Configuration

![image](https://user-images.githubusercontent.com/69375071/210247148-60ff5cf7-5f37-4e09-a077-fd6a8bf36eb8.png)

Use the following configuration for `vIOS-Ser-I`.

```
hostname vIOS-Ser-I
!
ip name-server 8.8.8.8
!
interface Loopback0
 ip address 10.1.1.4 255.255.255.255
 ip ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/0
 description Link1 to ASAv-I
 no switchport
 ip address 172.16.0.6 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/1
 description Link2 to ASAv-I
 no switchport
 ip address 172.16.0.18 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/2
 switchport access vlan 50
 switchport mode access
!
interface GigabitEthernet0/3
 switchport access vlan 50
 switchport mode access
!
interface GigabitEthernet1/0
 shutdown
!
interface GigabitEthernet1/1
 shutdown
!
interface GigabitEthernet1/2
 shutdown
!
interface GigabitEthernet1/3
 shutdown
!
interface Vlan50
 no shutdown
 ip address 172.16.50.254 255.255.255.0
 ip ospf message-digest-key 1 md5 #MyPass!034
!
router ospf 1
 router-id 10.1.1.4
 area 0 authentication message-digest
 passive-interface Vlan50
 network 10.1.1.4 0.0.0.0 area 0
 network 172.16.0.4 0.0.0.3 area 0
 network 172.16.0.16 0.0.0.3 area 0
 network 172.16.50.0 0.0.0.255 area 0
!
end
!
wr
!
```
