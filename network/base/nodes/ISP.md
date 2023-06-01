# ISP-I & II Configuration

![image](https://user-images.githubusercontent.com/69375071/210246480-b910772a-a95f-470a-a77a-7121460e63ac.png)

## ISP-I

Use the following configuration for `ISP-I`.

- No need to login. No username and no password by default.

```
hostname ISP-I
!
ip domain lookup
ip name-server 8.8.8.8
!
interface GigabitEthernet0/0
 no shutdown
 description Link to NAT1
 ip address dhcp
 ip nat outside
!
interface GigabitEthernet1/0
 no shutdown
 description Link to vIOS-Edge-I
 ip address 198.10.10.1 255.255.255.252
 ip nat inside
!
router bgp 64501
 network 0.0.0.0
 neighbor 198.10.10.2 remote-as 64500
 neighbor 198.10.10.2 password isp1md5pass
 neighbor 198.10.10.2 ttl-security hops 1
 neighbor 198.10.10.2 route-map static_default out
!
ip nat inside source list 1 interface GigabitEthernet0/0 overload
!
ip prefix-list static_default permit 0.0.0.0/0
!
route-map static_default permit 10
 match ip address prefix-list static_default
!
access-list 1 permit 195.1.1.0 0.0.0.255
access-list 1 permit 198.10.10.0 0.0.0.3
access-list 1 deny any
!
end
!
wr
y
!
```

## ISP-II

Use the following configuration for `ISP-II`.

```
hostname ISP-II
!
ip domain lookup
ip name-server 8.8.8.8
!
interface GigabitEthernet0/0
 no shutdown
 description Link to NAT2
 ip address dhcp
 ip nat outside
!
interface GigabitEthernet1/0
 no shutdown
 description Link to vIOS-Edge-I
 ip address 197.10.10.1 255.255.255.252
 ip nat inside
!
router bgp 64502
 network 0.0.0.0
 neighbor 197.10.10.2 remote-as 64500
 neighbor 197.10.10.2 password isp2md5pass
 neighbor 197.10.10.2 ttl-security hops 1
 neighbor 197.10.10.2 route-map static_default out
!
ip nat inside source list 1 interface GigabitEthernet0/0 overload
!
ip prefix-list static_default seq 5 permit 0.0.0.0/0
!
route-map static_default permit 10
 match ip address prefix-list static_default
!
access-list 1 permit 195.1.1.0 0.0.0.255
access-list 1 permit 197.10.10.0 0.0.0.3
access-list 1 deny any
!
end
!
wr
y
!
```
