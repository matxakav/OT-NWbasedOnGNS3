# Deploy ICS Network in GNS3 Server

- [Deploy ICS Network in GNS3 Server](#deploy-ics-network-in-gns3-server)
	- [ICS Network](#ics-network)
	- [Deploy ICS Network in GNS3 Server](#deploy-ics-network-in-gns3-server-1)
		- [1. Pull Docker Images into Ubuntu](#1-pull-docker-images-into-ubuntu)
		- [2. Import Additional Appliances into GNS3 Server](#2-import-additional-appliances-into-gns3-server)
		- [3. Instantiate and Configure Nodes](#3-instantiate-and-configure-nodes)
		- [4. Run and configure ONOS SDN controller](#4-run-and-configure-onos-sdn-controller)
		- [5. Connect Open vSwitches to ONOS](#5-connect-open-vswitches-to-onos)
		- [6. Use Auxiliary Console of a Docker Container](#6-use-auxiliary-console-of-a-docker-container)
		- [7. Capture the Traffic using Wireshark](#7-capture-the-traffic-using-wireshark)

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

Above all, you should complete [deployment of the generic base network](../base/README.md) in GNS3 server in your home desktop, because this ICS network is a fork of that generic base network.

### 1. Pull Docker Images into Ubuntu

Pull the docker images below into your home desktop where you install GNS3 server.

- Chinese users can use proxy to speed up the pulling. Refer to [this blog](https://blog.csdn.net/peng2hui1314/article/details/124267333) for three types of docker proxies.
- Special credit to Scada-LTS team for [their support](https://github.com/SCADA-LTS/Scada-LTS/issues/2473) in troubleshooting and creating the scadalts/scadalts:v2.6.18-mysql-8 docker image.

```sh
docker pull wzy318/kalilinux:latest
docker pull wzy318/openplc:latest
docker pull wzy318/scada-lts:latest
docker pull gns3/openvswitch:latest
docker pull onosproject/onos:2.7.0
```

### 2. Import Additional Appliances into GNS3 Server

[Refer to this document](./appliances/README.md)

### 3. Instantiate and Configure Nodes

[Refer to this document](./nodes/README.md)

### 4. Run and configure ONOS SDN controller

[Refer to this document](./nodes/ONOS.md)

### 5. Connect Open vSwitches to ONOS

We use the console of OVS (Open vSwitch) to connect to ONOS SDN controller. Double click a running OVS node to open its console.

- An OVS node is basically a Linux with Open vSwitch program installed.
- The console of OVS node is a Linux shell `/bin/sh`.
- An OVS node calls the Open vSwitch program using `ovs-vsctl` and `ovs-ofctl` commands.

We can list all bridges, interfaces, and controllers of an OVS.

```sh
ovs-vsctl show
```

We connect the bridge 0 (`br0`) of OVS to ONOS SDN controller over OpenFlow/TCP protocol. Since our ONOS ver2.7.0 uses OpenFlow ver1.3, then we make the OVS node use OpenFlow13 as well.

- The ONOS Docker container must be running with OpenFlow app components activated.
- In most cases, the IP of ONOS Docker container is `172.17.0.2`. You can run `docker network inspect bridge` in the home desktop to check ONOS's IP.
- OpenFlow ver1.5 (latest) is not compatible with ver1.3
- ONOS SDN controller uses port `6633` for OpenFlow/TCP, and port `6653` for OpenFlow over SSL/TLS. It's advised to [set up SSL/TLC using self-signed certs](https://wiki.onosproject.org/pages/viewpage.action?pageId=6358090) if the connection between OVS and ONOS is over the public network. However,  we don't need it in our `docker0` case.

```sh
ovs-vsctl set bridge br0 protocols=OpenFlow13
ovs-vsctl set-controller br0 tcp:172.17.0.2:6633
```

After that, we can check if the connection established using `ovs-vsctl show`. We can notice that "`is_connected: true`".

![image](../../assets/ovs-vsctl%20show.png)

In OVS, we can remove the connection of SDN controller using the following command. Note that this will remove all OpenFlow connections in `br0`.

```sh
ovs-vsctl del-controller br0
```

The ONOS SDN controller can deploy flow entries into connected OVS nodes. We can echo these flow entries in OVS console.

- This command is useful whenever we start the whole project. We dump flows to ensure that all connected OVS nodes have flow entries deployed by ONOS.

```sh
ovs-ofctl -O OpenFlow13 dump-flows br0
```

Disconnected OVS nodes must have a default flow entry with action "NORMAL". This action is [implemented by OVS itself](https://docs.openvswitch.org/en/latest/ref/ovs-actions.7/#the-ovs-normal-pipeline) and serves like the "Reactive Forwarding" app component in ONOS.

![image](../../assets/NORMAL%20Flow.png)

Connected OVS nodes may have some flow entries deployed by some activated app components of ONOS.

- Sometimes newly connected OVS nodes don't have flow entries. That's because our app component fails to detect these new OVS nodes. In this case, we can deactivate and reactivate our app components in ONOS to make it work.

![image](../../assets/ICS%20Flow.png)

### 6. Use Auxiliary Console of a Docker Container

GNS3 server provides an auxiliary console for each docker container, which not only provides quick access to a container's console, but also provides some useful tools such as `ip` and `wget` that the container may not have.

To enter the auxiliary console, right click on a docker container and select `Auxiliary console`.

- You can speed up the [configuration of Scada-LTS1](./nodes/Scada-LTS1.md) using the auxiliary console instead of SSH.
- You can quick access the console of OpenPLC1 and Scada-LTS1 using the auxiliary console. Although OpenPLC1 and Scada-LTS1 don't have the `wget` tool, we can use the `wget` in auxiliary console to quickly download something into them.

![image](../../assets/Auxiliary%20Console.png)

### 7. Capture the Traffic using Wireshark

Wireshark is a well-known network protocol analyzer around the globe. Refer to the [official user guide](https://www.wireshark.org/docs/wsug_html_chunked/) for more information.

GNS3 server in your home desktop already has a Wireshark "server" integrated, however, you need a Wireshark "client" in your laptop to visualise the traffic. [Download](https://www.wireshark.org/download.html) and install Wireshark in your laptop.

- If you are a Mac user with [Homebrew](https://brew.sh) package manager installed. You can install Wireshark as a Homebrew cask.
  - Chinese users often need a proxy to use Homebrew.

```sh
brew install --cask wireshark
```

We first start all nodes in ICS network so that there is traffic among them.

Then, right click on a target link and select `Start capture` to cpature the link. A Wireshark window should launch in your laptop, showing all packets that go through the link.

Sometimes when the Wireshark window in laptop won't launch, right click on the link being captured and select `Start Wireshark` to launch it.

When the nodes at both ends of the link are powered off, the capturing of the link automatically stops.

<div align=center>

<img src="../../assets/Start%20Capture.png" width="48%" />
<img src="../../assets/Start%20Wireshark.png" width="50%" />

</div>
