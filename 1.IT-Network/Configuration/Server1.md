# Server1 Configuration

> It's advised to complete configuring all other devices before `Server1` and `Serv-DMZ-I`.

![image](https://user-images.githubusercontent.com/69375071/210246893-35e9d051-41e5-4f23-b967-3cb49b5035d0.png)

## Adjusting Links

1. Temporarily adjust the links to ensure `Server1` has a valid Internet connection. Note that we choose interface `e0`.

TODO image1

## Server1 Configuration

2. Login with username `ubuntu` and password `ubuntu`. The network is pre-configured and the server should have Internet access now. You can verify it using `ping amazon.com`.
3. Switch to root user and load a bonding module to Linux kernel.

```sh
sudo su
cd /
modprobe bonding
echo "bonding" >> /etc/modules
```

4. Install dependencies via `apt` package manager and reset the failed service `isc-dhcp-server`. We configure these services one by one as follows.

```sh
apt update
apt install bind9 ntp isc-dhcp-server freeradius syslog-ng
systemctl reset-failed
```

### DNS: `bind9`

### NTP: `ntp`

### DHCP: `isc-dhcp-server`

### FreeRADIUS: `freeradius`

### Syslog-ng: `syslog-ng`

### Bonding: `netplan`

2. Check the network status. We find that both `ens3` and `ens4` are in `state DOWN`, which means no Internet connection.

```console
ip a
```
![image](https://user-images.githubusercontent.com/69375071/211831061-3ac6b5f7-334f-46c8-99c7-ec2fd07bf2ad.png)

3. Switch to root user and load a bonding module to Linux kernel.

```console
sudo su
modprobe bonding
echo "bonding" >> /etc/modules
```

4. Edit netplan to establish valid Internet connection.

> I assume you know how to use vim editor. Use `i` to enter `INSERT` mode, `ESC` to back to default mode, and `:wq` to save and exit.

```console
vim /etc/netplan/50-cloud-init.yaml
```

```yaml
network:
    ethernets:
        ens3:
            dhcp4: false
        ens4:
            dhcp4: false
    bonds:
        bond0:
            addresses:
                - 172.16.50.1/24
            routes:
                - to: default
                  via: 172.16.50.254
            nameservers:
                addresses:
                    - 172.16.50.1
            interfaces:
                - ens3
                - ens4
            parameters:
                mode: balance-rr
                mii-monitor-interval: 100
    version: 2
```

5. Apply the netplan. You can check by `ip a` that both `ens3` and `ens4` are `UP` and have a master `bond0`.

```console
netplan apply
```

6. 
