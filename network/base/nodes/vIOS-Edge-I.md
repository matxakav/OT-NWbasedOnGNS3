# vIOS-Edge-I Configuration

![image](https://user-images.githubusercontent.com/69375071/210247101-bd4574b5-01cc-4319-a11d-61ff599bb425.png)

Use the following configuration for `vIOS-Edge-I`.

```
hostname vIOS-Edge-I
!
ip name-server 8.8.8.8
!
interface Loopback0
 description Management
 ip address 10.1.1.5 255.255.255.255
!
interface GigabitEthernet0/0
 no shutdown
 description Link to ASAv-DMZ-I
 ip address 195.1.1.129 255.255.255.252
 ip nat outside
!
interface GigabitEthernet0/1
 no shutdown
 description Link to ISP-I
 ip address 198.10.10.2 255.255.255.252
 ip nat outside
!
interface GigabitEthernet0/2
 no shutdown
 description Link to ASAv-I
 ip address 172.16.0.2 255.255.255.252
 ip nat inside
!
interface GigabitEthernet0/3
 no shutdown
 description Link to ISP-II
 ip address 197.10.10.2 255.255.255.252
 ip nat outside
!
router bgp 64500
 network 195.1.1.0
 neighbor isp-group peer-group
 neighbor isp-group ttl-security hops 1
 neighbor isp-group filter-list 10 out
 neighbor 197.10.10.1 remote-as 64502
 neighbor 197.10.10.1 peer-group isp-group
 neighbor 197.10.10.1 password isp2md5pass
 neighbor 198.10.10.1 remote-as 64501
 neighbor 198.10.10.1 peer-group isp-group
 neighbor 198.10.10.1 password isp1md5pass
 neighbor 198.10.10.1 route-map setlocalin in
!
ip as-path access-list 10 permit ^$
!
ip nat pool 1 195.1.1.1 195.1.1.127 netmask 255.255.255.128
ip nat inside source list 1 pool 1 overload
!
ip route 10.0.0.0 255.0.0.0 172.16.0.1
ip route 172.16.0.0 255.255.0.0 172.16.0.1
ip route 192.168.0.0 255.255.192.0 172.16.0.1
ip route 195.1.1.0 255.255.255.0 Null0
ip route 195.1.1.128 255.255.255.128 195.1.1.130
!
route-map setlocalin permit 10
 set local-preference 150
!
access-list 1 permit 192.168.10.0 0.0.0.255
access-list 1 permit 192.168.20.0 0.0.0.255
access-list 1 permit 192.168.30.0 0.0.0.255
access-list 1 permit 192.168.40.0 0.0.0.255
access-list 1 permit 10.0.0.0 0.0.0.255
access-list 1 permit 10.1.1.0 0.0.0.255
access-list 1 permit 172.16.0.0 0.0.0.255
access-list 1 permit 172.16.50.0 0.0.0.255
access-list 1 deny any
!
end
!
wr
!
```
