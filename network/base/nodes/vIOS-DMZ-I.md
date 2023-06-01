# vIOS-DMZ-I Configuration

![image](https://user-images.githubusercontent.com/69375071/210247052-aa524a52-8186-49d4-963e-60ae35945b0a.png)

Use the following configuration for `vIOS-DMZ-I`.

```
hostname vIOS-DMZ-I
!
ip name-server 8.8.8.8
!
interface GigabitEthernet0/0
 description Link to ASAv-DMZ-I
 no switchport
 ip address 195.1.1.134 255.255.255.252
!
interface GigabitEthernet0/1
 switchport access vlan 10
 switchport mode access
!
interface GigabitEthernet0/2
 shutdown
!         
interface GigabitEthernet0/3
 shutdown
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
interface Vlan10
 no shutdown
 ip address 195.1.1.166 255.255.255.248
!
ip route 0.0.0.0 0.0.0.0 195.1.1.133
!
end
!
wr
!
```
