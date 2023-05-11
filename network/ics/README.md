# Deploy ICS Network in GNS3 Server

## Network Description

![image](/assets/ICS%20Network.png)

The OT network is forked and developed from the IT network, where all VPCS's are replaced by docker containers (i.e., OpenPLC, ScadaLTS, and KaliLinux). The reasons why we use docker containers instead of [ubuntu cloud images](https://cloud-images.ubuntu.com) to simulate these end devices are as follows.

- GNS3 server has native support for docker containers, including port mapping and network configuration.
- Docker containers are lightweight and consume less RAM, ideal for deployment in cloud computing instances.

> Docker containers in GNS3 server are `versatile`. They are created on project opening and removed on project closing, so all changes made to the containers are not persistent.

## Deployment of ICS Network

Pull the required docker images.

```sh
docker pull wzy318/kalilinux:latest
docker pull wzy318/openplc:latest
docker pull wzy318/scada-lts:latest
docker pull gns3/openvswitch:latest
docker pull onosproject/onos:2.7.0
```

> Special credit to Scada-LTS team for their support in [troubleshooting and creating](https://github.com/SCADA-LTS/Scada-LTS/issues/2473) of scadalts/scadalts:v2.6.18-mysql-8 docker image.

Create templates for these 3 docker images following the table. Keep other unspecified fields unchanged.

| Docker image | Name | Console type | HTTP port in the container | HTTP path |
|-|-|-|-|-|
| scadalts/scadalts:v2.6.18-mysql-8 | ScadaLTS | http | 8080 | /Scada-LTS |
| sflorenz05/open-plc:v0.3 | OpenPLC | http | 8090 | / |
| wzy318/kalilinux:latest | KaliLinux | telnet | | |

| | |
|-|-|
| ![image](https://user-images.githubusercontent.com/69375071/219404831-48619037-2910-45de-985f-01f51ac5617b.png) | ![image](https://user-images.githubusercontent.com/69375071/219404968-3a93abe1-d846-4ca6-aab9-1a6dd46daac4.png) |

Link the network following the image at the top.

Configure network interfaces for the docker containers according to the table below.

1. Right click on the device and select `Edit config`.
2. Uncomment the lines as shown in the picture to configure a static IPv4 address for the default network interface `eth0`. All docker containers share a netmask `255.255.255.0` and nameserver `8.8.8.8`.

| Docker container | address | gateway |
|-|-|-|
| OpenPLC-1 | 192.168.10.1 | 192.168.10.254 |
| OpenPLC-2 | 192.168.20.1 | 192.168.20.254 |
| OpenPLC-3 | 192.168.30.1 | 192.168.30.254 |
| KaliLinux | 192.168.40.1 | 192.168.40.254 |
| ScadaLTS | 172.16.50.1 | 172.16.50.254 |

| | |
|-|-|
| ![image](https://user-images.githubusercontent.com/69375071/219405156-cff06161-ff39-49bb-bf13-9c45d956dbdc.png) | ![image](https://user-images.githubusercontent.com/69375071/219405296-ad4e567d-4192-43ee-adec-6d8b8838c263.png) |
