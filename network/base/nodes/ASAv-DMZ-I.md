# ASAv-DMZ-I

![image](https://user-images.githubusercontent.com/69375071/210246350-34ba69d1-b039-4500-b0d8-9db66c9e54da.png)

Use the following configuration for `ASAv-DMZ-I`.

- You should set an enable password upon the first start of ASAv nodes. We use the password `ciscocisco` in our ASAv-DMZ-I.
  - The enable password will prompt every time when you try to enter the "privileged mode" using `en`.
- You need to choose whether to enable anonymous error reporting for the first time when you enter the "config mode".

```
hostname ASAv-DMZ-I
!
interface GigabitEthernet0/0
 no shutdown
 description Link to vIOS-Edge-I
 nameif OUTSIDE
 security-level 0
 ip address 195.1.1.130 255.255.255.252
!
interface GigabitEthernet0/1
 no shutdown
 description Link to vIOS-DMZ-I
 nameif INSIDE
 security-level 100
 ip address 195.1.1.133 255.255.255.252
!
dns domain-lookup OUTSIDE
dns server-group
 name-server 8.8.8.8
!
object network dmz_server
 host 195.1.1.161
object network public_addr
 subnet 195.1.1.0 255.255.255.0
object network vios_edge
 host 195.1.1.129
object network dns1
 host 8.8.8.8
object network dns2
 host 8.8.4.4
object-group network dns
 network-object object dns1
 network-object object dns2
!
access-group out-to-ins in interface OUTSIDE
!
access-list out-to-ins extended permit icmp object public_addr object dmz_server
access-list out-to-ins extended permit udp object-group dns object dmz_server eq domain
access-list out-to-ins extended permit tcp any object dmz_server eq www
access-list out-to-ins extended permit tcp any object dmz_server eq https
!
route OUTSIDE 0.0.0.0 0.0.0.0 195.1.1.129
route INSIDE 195.1.1.160 255.255.255.224 195.1.1.134
route INSIDE 195.1.1.192 255.255.255.192 195.1.1.134
!
policy-map global_policy
 class inspection_default
  inspect icmp
  inspect http
!
end
!
wr
!
```
