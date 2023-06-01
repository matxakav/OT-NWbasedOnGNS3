# vIOS-Core-I & II Configuration

![image](https://user-images.githubusercontent.com/69375071/210247015-b6359b9b-2b75-4bc6-a59f-ff6dd0b90518.png)

## vIOS-Core-I

Use the following configuration for `vIOS-Core-I`.

- The vIOS console is quite verbose.
- No need to login. No username and no password by default.

```
hostname vIOS-Core-I
!
ip name-server 8.8.8.8
!
interface Loopback0
 ip address 10.1.1.1 255.255.255.255
 ip ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/0
 description Link to vIOS-Core-II
 no switchport
 ip address 10.0.0.5 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/1
 description Link to vEOS-Dis-II
 no switchport
 ip address 10.0.0.18 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/2
 description Link to vEOS-Dis-I
 no switchport
 ip address 10.0.0.10 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/3
 description Link to ASAv-I
 no switchport
 ip address 172.16.0.10 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
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
router ospf 1
 router-id 10.1.1.1
 area 0 authentication message-digest
 network 10.0.0.4 0.0.0.3 area 0
 network 10.0.0.8 0.0.0.3 area 0
 network 10.0.0.16 0.0.0.3 area 0
 network 10.1.1.1 0.0.0.0 area 0
 network 172.16.0.8 0.0.0.3 area 0
!
end
!
wr
!
```

## vIOS-Core-II

Use the following configuration for `vIOS-Core-II`.

```
hostname vIOS-Core-II
!
ip name-server 8.8.8.8
!
interface Loopback0
 ip address 10.1.1.2 255.255.255.255
 ip ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/0
 description Link to vIOS-Core-I
 no switchport
 ip address 10.0.0.6 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/1
 description Link to vEOS-Dis-I
 no switchport
 ip address 10.0.0.22 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/2
 description Link to vEOS-Dis-II
 no switchport
 ip address 10.0.0.14 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
 ip ospf network point-to-point
!
interface GigabitEthernet0/3
 description Link to ASAv-I
 no switchport
 ip address 172.16.0.14 255.255.255.252
 ip ospf message-digest-key 1 md5 #MyPass!034
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
router ospf 1
 router-id 10.1.1.2
 area 0 authentication message-digest
 network 10.0.0.4 0.0.0.3 area 0
 network 10.0.0.12 0.0.0.3 area 0
 network 10.0.0.20 0.0.0.3 area 0
 network 10.1.1.2 0.0.0.0 area 0
 network 172.16.0.12 0.0.0.3 area 0
!
end
!
wr
!
```
