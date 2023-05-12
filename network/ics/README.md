# Deploy ICS Network in GNS3 Server

- [Deploy ICS Network in GNS3 Server](#deploy-ics-network-in-gns3-server)
	- [ICS Network](#ics-network)
	- [Deployment of ICS Network](#deployment-of-ics-network)

The ICS network is based on the generic base network.

```
ics_network = generic_base_network
	- 3 * vpcs
	+ openplc
	+ scada-lts
	+ 2 * kalilinux
	+ 4 * open_vswitch
	+ onos
```

Portable project of the generic base network is available on [Google Drive](https://drive.google.com/drive/folders/1ooRXrQyf2Lz0mGtcHeRZjTrhDW_SKV4w/).

- You can skip this guide by downloading the portable project and open it via GNS3 client.
- Read this guide if you want to customise your ICS network in GNS3 server.

## ICS Network

![image](/assets/Base%20Network.png)

The figure is the generic base network deployed in GNS3 server.

![image](/assets/ICS%20Network.png)

The figure is the ICS network deployed in GNS3 server.

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

Create templates for these docker images following the table. Keep other unspecified fields unchanged.

| Docker image | Name | Console type | HTTP port in the container | HTTP path |
|-|-|-|-|-|
| wzy318/kalilinux:latest | KaliLinux | telnet | | |
| wzy318/openplc:latest | OpenPLC | http | 8080 | / |
| wzy318/scadalts:latest | Scada-LTS | http | 8080 | /Scada-LTS |
| gns3/oprnvswitch:latest | Open vSwitch | telnet | | |


| | |
|-|-|
| ![image](https://user-images.githubusercontent.com/69375071/219404831-48619037-2910-45de-985f-01f51ac5617b.png) | ![image](https://user-images.githubusercontent.com/69375071/219404968-3a93abe1-d846-4ca6-aab9-1a6dd46daac4.png) |

Link the network following the image at the top.

Configure network interfaces for the docker containers according to the table below.

1. Right click on the device and select `Edit config`.
2. Uncomment the lines as shown in the picture to configure a static IPv4 address for the default network interface `eth0`. All docker containers share a name server `8.8.8.8`.

| Docker container | address/mask | gateway |
|-|-|-|
| OpenPLC1 | 192.168.10.1/24 | 192.168.10.254 |
| KaliLinux1 | 192.168.40.1/24 | 192.168.40.254 |
| Scada-LTS1 | 172.16.50.1/24 | 172.16.50.254 |
| KaliLinux2 | 192.168.50.2/24 | 172.16.50.254 |
| OVS-I | 172.17.1.1/16 | 172.17.0.1 |
| OVS-II | 172.17.1.2/16 | 172.17.0.1 |
| OVS-III | 172.17.1.3/16 | 172.17.0.1 |
| OVS-IV | 172.17.1.4/16 | 172.17.0.1 |

| | |
|-|-|
| ![image](https://user-images.githubusercontent.com/69375071/219405156-cff06161-ff39-49bb-bf13-9c45d956dbdc.png) | ![image](https://user-images.githubusercontent.com/69375071/219405296-ad4e567d-4192-43ee-adec-6d8b8838c263.png) |

Create ONOS SDN controller using following command. It starts ONOS with a web UI listening at TCP:127.0.0.1:8181 and a SSH console listening at TCP:127.0.0.1:8101.

```sh
docker run --restart=unless-stopped -d -p 127.0.0.1:8181:8181 -p 127.0.0.1:8101:8101 --name onos onosproject/onos:2.7.0
```

Connect Open vSwitch to ONOS

```sh
ovs-vsctl set bridge br0 protocols=OpenFlow13
ovs-vsctl set-controller br0 tcp:172.17.0.2:6633
```

Dump flow entries from ONOS

```sh
ovs-ofctl -O OpenFlow13 dump-flows br0
```