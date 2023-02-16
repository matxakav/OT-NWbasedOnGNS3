# Experimental OT Network on GNS3

## Network Description

![image](/img/1.png)

The OT network is forked and developed from the IT network, where all VPCS's are replaced by docker containers (i.e., OpenPLC, ScadaLTS, and KaliLinux). The reasons why we use docker containers instead of [ubuntu cloud images](https://cloud-images.ubuntu.com) to simulate these end devices are as follows.

- GNS3 server has native support for docker containers, including port mapping and network configuration.
- Docker containers are lightweight and consume less RAM, ideal for deployment in cloud computing instances.

> Docker containers in GNS3 server are `versatile`. They are created on project opening and removed on project closing, so all changes made to the containers are not persistent.

## Prerequisites

Pull the required docker images.

```bash
docker pull scadalts/scadalts:v2.6.18-mysql-8
docker pull sflorenz05/open-plc:v0.3
docker pull wzy318/kalilinux:latest
```

Create templates for these 3 docker images following the table. Keep other unspecified fields unchanged.

| Docker image | Name | Console type | HTTP port in the container | HTTP path |
|-|-|-|-|-|
| scadalts/scadalts:v2.6.18-mysql-8 | ScadaLTS | http | 8080 | /Scada-LTS |
| sflorenz05/open-plc:v0.3 | OpenPLC | http | 8090 | / |
| wzy318/kalilinux:latest | KaliLinux | telnet | | |

| | |
|-|-|
| ![image](/img/2.png) | ![image](/img/3.png) |

Link the network following the image above.

Configure network interfaces for the docker containers.

1. Right click on the device and select `Edit config`.
2. Uncomment the following lines to configure a static IPv4 address.

```
auto eth0
iface eth0 inet static
	address 192.168.10.1
	netmask 255.255.255.0
	gateway 192.168.10.254
	up echo nameserver 8.8.8.8 > /etc/resolv.conf
```

| | |
|-|-|
| ![image](/img/4.png) | ![image](/img/5.png) |

| Docker container | address | gateway |
|-|-|-|
| OpenPLC-1 | 192.168.10.1 | 192.168.10.254 |
| OpenPLC-2 | 192.168.20.1 | 192.168.20.254 |
| OpenPLC-3 | 192.168.30.1 | 192.168.30.254 |
| KaliLinux | 192.168.40.1 | 192.168.40.254 |
| ScadaLTS | 172.16.50.1 | 172.16.50.254 |

