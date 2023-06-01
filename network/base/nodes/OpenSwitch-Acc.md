# OpenSwitch-Acc-I & II Configuration

![image](https://user-images.githubusercontent.com/69375071/210249340-0f4eab71-cb6c-41c2-b9e4-4e8c8be6caa5.png)

## OpenSwitch-Acc-I

All Cisco nodes share a very similar configuration approach, so we only detail the configuration steps of `OpenSwitch-Acc-I`.

1. Power on the node `OpenSwitch-Acc-I` and await about half a minute for it to start. Then, double click the node (or right click it and select `Console`) to enter its serial console via telnet.
2. Login with username `netop` and password `netop`. The password won't be echoed.
3. Enter the "privileged mode" and then enter the "config mode" using `en` and then `conf t`.
   - The console of a Cisco appliance have 4 modes: user mode, privileged mode, config mode, and sub-config mode. For more information, read [this blog](https://blog.csdn.net/baidu_39274578/article/details/113359970) in Chinese and [this blog](https://blog.router-switch.com/2012/08/four-modes-to-access-and-configure-a-cisco-router/) in English.
   - Note the difference between "privileged mode" and "config mode". The "config mode" must have the hostname appended by `(config)#`.
   - All Cisco nodes need `en` and `conf t` before configuration.
   - You can use `exit` to exit to the previous mode. You should `exit` twice from "config mode" to "user mode".

```
en
conf t
```

![image](https://user-images.githubusercontent.com/69375071/210293414-ca1baf79-ec91-413e-aea1-e922cc6e0b85.png)

4. Copy and paste the following configuration commands into the console. The console will exit to "privileged mode" after these commands.
   - The character `!` is for comments. All `!` lines are ignored by Cisco appliance.
   - The `end` command will bring you directly to "privileged mode" from "config mode" or "sub-config mode". As it implies, to end the configuration process.

```
hostname OpenSwitch-Acc-I
!
vlan 10
    no shutdown
vlan 20
    no shutdown
interface eth1
    no shutdown
    no routing
    vlan trunk allowed 10,20
interface eth2
    no shutdown
    no routing
    vlan trunk allowed 10,20
interface eth3
    no shutdown
    no routing
    vlan access 10
interface eth4
    no shutdown
    no routing
    vlan access 20
interface vlan20
    no shutdown
    ip address 192.168.20.250/24
interface mgmt
    ip static 10.1.1.9/30
    default-gateway 10.1.1.10
    nameserver 8.8.8.8
ip route 0.0.0.0/0 192.168.20.254
!
end
!
```

5. Now, the node is configured in RAM and will be reset after reboot. To make the configuration permanent (write the configuration to storage), we copy `running-config` to `startup-config` in the "privileged mode".
   - You can use `sh run` in the "privileged mode" to print all running-config in the console. This is useful when you want to recheck your configuration.
   - You can't save your configuration or show running-config in the "config mode".

```
copy running-config startup-config
```

## OpenSwitch-Acc-II

Use the following configuration commands for `OpenSwitch-Acc-II`, following the same configuration steps above.

- Note that we already add the line "`copy running-config startup-config`" at the end of these commands, so you don't need to run it again.
- We assume you know that all Cisco nodes require `en` and `conf t` to enter the "config mode" before pasting their configuration commands.

```
hostname OpenSwitch-Acc-II
!
vlan 30
    no shutdown
vlan 40
    no shutdown
interface eth1
    no shutdown
    no routing
    vlan trunk allowed 30,40
interface eth2
    no shutdown
    no routing
    vlan trunk allowed 30,40
interface eth3
    no shutdown
    no routing
    vlan access 30
interface eth4
    no shutdown
    no routing
    vlan access 40
interface vlan40
    no shutdown
    ip address 192.168.40.250/24
interface mgmt
    ip static 10.1.1.13/30
    default-gateway 10.1.1.14
    nameserver 8.8.8.8
ip route 0.0.0.0/0 192.168.40.254
!
end
!
copy running-config startup-config
!
```
