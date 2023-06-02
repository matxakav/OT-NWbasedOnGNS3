# Deploy ICS Network in GNS3 Server

- [Deploy ICS Network in GNS3 Server](#deploy-ics-network-in-gns3-server)
	- [ICS Network](#ics-network)
	- [Deploy ICS Network in GNS3 Server](#deploy-ics-network-in-gns3-server-1)
		- [Pull Docker Images into Ubuntu](#pull-docker-images-into-ubuntu)
		- [Import Additional Appliances into GNS3 Server](#import-additional-appliances-into-gns3-server)
		- [Instantiate and Configure Nodes](#instantiate-and-configure-nodes)
		- [Start and configure ONOS SDN controller](#start-and-configure-onos-sdn-controller)
		- [Connect Open vSwitches to ONOS](#connect-open-vswitches-to-onos)

Portable project of the ICS network is available on [Google Drive](https://drive.google.com/drive/folders/1ooRXrQyf2Lz0mGtcHeRZjTrhDW_SKV4w/).

- You can skip this guide by downloading the portable project and open it via GNS3 client.
- Read this guide if you want to customise your ICS network in GNS3 server.

## ICS Network

<br/>

![image](../../assets/ICS%20Network.png)

<p align="center"><b>Figure 1</b> Generic Base Network</p>

<br/>

Figure 1 shows the topology of an ICS network. The ICS network is based on our generic base network. We remove 3 VPCS nodes and then add 1 OpenPLC, 1 Scada-LTS, 2 Kali Linux nodes, 4 Open vSwitch nodes, and 1 ONOS SDN controller.

## Deploy ICS Network in GNS3 Server

Above all, you should complete [deployment of the generic base network](../base/README.md) in GNS3 server in your home desktop, because this ICS network is a fork of the generic base network.

### Pull Docker Images into Ubuntu

Pull the docker images below into your home desktop where you install GNS3 server.

- Chinese users can use proxy to speed up the pulling. Refer to [this blog](https://blog.csdn.net/peng2hui1314/article/details/124267333) for three types of docker proxies.
- Special credit to Scada-LTS team for their support in [troubleshooting and creating](https://github.com/SCADA-LTS/Scada-LTS/issues/2473) of scadalts/scadalts:v2.6.18-mysql-8 docker image.

```sh
docker pull wzy318/kalilinux:latest
docker pull wzy318/openplc:latest
docker pull wzy318/scada-lts:latest
docker pull gns3/openvswitch:latest
docker pull onosproject/onos:2.7.0
```

### Import Additional Appliances into GNS3 Server

[Refer to this document](./appliances/README.md)

### Instantiate and Configure Nodes

[Refer to this document](./nodes/README.md)

### Start and configure ONOS SDN controller

Create ONOS SDN controller using following command. It starts ONOS with a web UI listening at TCP:127.0.0.1:8181 and a SSH console listening at TCP:127.0.0.1:8101.

```sh
docker run --restart=unless-stopped -d -p 127.0.0.1:8181:8181 -p 127.0.0.1:8101:8101 --name onos onosproject/onos:2.7.0
```

### Connect Open vSwitches to ONOS

```sh
ovs-vsctl set bridge br0 protocols=OpenFlow13
ovs-vsctl set-controller br0 tcp:172.17.0.2:6633
```

Dump flow entries in Open vSwitch from ONOS

```sh
ovs-ofctl -O OpenFlow13 dump-flows br0
```
