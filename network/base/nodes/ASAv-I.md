# ASAv-I

![image](https://user-images.githubusercontent.com/69375071/210246415-962006be-4427-41ac-a280-51395ebde498.png)

ASAv is the previous generation Cisco firewall, which is now replaced by Cisco Firepower. However, ASAv is sufficient and lightweight for our experiments.

The Cisco ASAv firewall is unlicenced and require one smart licence to remove its restrictions on bandwidth and connections. In ASAv console, use `sh ver` in "privileged mode" to print more details.

- You should set an enable password upon the first start of ASAv nodes. We use the password `ciscocisco` in our ASAv-I.
  - The enable password will prompt every time when you try to enter the "privileged mode" using `en`.
- You need to choose whether to enable anonymous error reporting for the first time when you enter the "config mode".

Use the following configuration for `ASAv-I`.

- It's quite weird that very rarely ASAv-I will lose the OSPF configuration part (four network in `router ospf 1`) on importing, exporting, or duplicating the whole project. I don't know if this is a bug of ASAv or GNS3 server. Anyway, use `show run` to recheck the ASAv-I configuration after you migrate the project.
- You may want to modify the Access Control List (ACL) in ASAv-I. Refer to [Brezular's blog](https://brezular.com/2017/12/01/enterprise-network-on-gns3-part-4-cisco-asav-i/) to get started, and read [Cisco's official document](https://www.cisco.com/c/en/us/td/docs/security/asa/asa92/configuration/general/asa-general-cli/acl-extended.pdf) for advanced usage.

```
hostname ASAv-I
!
zone inside_zone
zone server_zone
!
interface GigabitEthernet0/0
 no shutdown
 description Link to vIOS-Core-II
 nameif INSIDE0
 security-level 100
 zone-member inside_zone
 ip address 172.16.0.13 255.255.255.252
 ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/1
 no shutdown
 description Link to vIOS-Core-I
 nameif INSIDE1
 security-level 100
 zone-member inside_zone
 ip address 172.16.0.9 255.255.255.252
 ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/2
 no shutdown
 description Link to vIOS-Edge-I
 nameif OUTSIDE
 security-level 0
 ip address 172.16.0.1 255.255.255.252
!
interface GigabitEthernet0/3
 no shutdown
 description Link1 to vIOS-Ser-I
 nameif SERVER0
 security-level 50
 zone-member server_zone
 ip address 172.16.0.5 255.255.255.252
 ospf message-digest-key 1 md5 #MyPass!034
!
interface GigabitEthernet0/4
 no shutdown
 description Link2 to vIOS-Ser-I
 nameif SERVER1
 security-level 50
 zone-member server_zone
 ip address 172.16.0.17 255.255.255.252
 ospf message-digest-key 1 md5 #MyPass!034
!
dns domain-lookup OUTSIDE
dns server-group
 name-server 8.8.8.8
!
object network vlan10_192.168.10
 subnet 192.168.10.0 255.255.255.0
object network vlan20_192.168.20
 subnet 192.168.20.0 255.255.255.0
object network vlan30_192.168.30
 subnet 192.168.30.0 255.255.255.0
object network vlan40_192.168.40
 subnet 192.168.40.0 255.255.255.0
object network vlan50_172.16.50
 subnet 172.16.50.0 255.255.255.0
object network loopbacks
 subnet 10.1.1.0 255.255.255.0
object network dns1
 host 8.8.8.8
object network dns2
 host 8.8.4.4
object-group network end_vlans
 network-object object vlan10_192.168.10
 network-object object vlan20_192.168.20
 network-object object vlan30_192.168.30
 network-object object vlan40_192.168.40
object-group network server_vlans
 network-object object vlan50_172.16.50
object-group network dns
 network-object object dns1
 network-object object dns2
!
access-group out-to-ins in interface OUTSIDE
access-group server-to-in_out in interface SERVER0
access-group server-to-in_out in interface SERVER1
!
access-list out-to-ins extended permit udp object-group dns object-group end_vlans eq domain
access-list out-to-ins extended permit udp object-group dns object-group server_vlans eq domain
!
access-list server-to-in_out extended permit icmp object-group server_vlans any
access-list server-to-in_out extended permit udp object-group server_vlans object-group dns eq domain
access-list server-to-in_out extended permit tcp object-group server_vlans any eq www
access-list server-to-in_out extended permit tcp object-group server_vlans any eq https
access-list server-to-in_out extended permit tcp object-group server_vlans object-group end_vlans eq 502
!
icmp permit 192.168.10.0 255.255.255.0 INSIDE0
icmp permit 192.168.20.0 255.255.255.0 INSIDE0
icmp permit 192.168.30.0 255.255.255.0 INSIDE0
icmp permit 192.168.40.0 255.255.255.0 INSIDE0
icmp permit 10.1.1.0 255.255.255.0 INSIDE0
icmp permit 192.168.10.0 255.255.255.0 INSIDE1
icmp permit 192.168.20.0 255.255.255.0 INSIDE1
icmp permit 192.168.30.0 255.255.255.0 INSIDE1
icmp permit 192.168.40.0 255.255.255.0 INSIDE1
icmp permit 10.1.1.0 255.255.255.0 INSIDE1
icmp permit 172.16.50.0 255.255.255.0 SERVER0
icmp permit 172.16.50.0 255.255.255.0 SERVER1
!
router ospf 1
 router-id 10.1.1.3
 network 172.16.0.4 255.255.255.252 area 0
 network 172.16.0.8 255.255.255.252 area 0
 network 172.16.0.12 255.255.255.252 area 0
 network 172.16.0.16 255.255.255.252 area 0
 area 0 authentication message-digest
 default-information originate
!
route OUTSIDE 0.0.0.0 0.0.0.0 172.16.0.2
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
