# Instantiate and Configure Nodes

- [Instantiate and Configure Nodes](#instantiate-and-configure-nodes)
	- [Configure GNS3 Client Workspace](#configure-gns3-client-workspace)
	- [Map Nodes to Appliances](#map-nodes-to-appliances)
		- [Rename a Node](#rename-a-node)
		- [Configure Network Adapters](#configure-network-adapters)
		- [Configure Cloud Nodes](#configure-cloud-nodes)
	- [Connect Nodes](#connect-nodes)
	- [Configure Nodes](#configure-nodes)

The word "instantiate" is a Java-style expression to `new` an object from a given class. In GNS3 server, it describes the action to create a node from a given appliance.

## Configure GNS3 Client Workspace

When you create a new project, it's advised to enable `Show the grid`, `Snap to grid`, and `Show interface labels` from the menu bar of GNS3 client. You can use `Fit in view` to automatically zoom the workspace to display all nodes.

![image](https://user-images.githubusercontent.com/69375071/210214869-4f799ce6-8802-4d91-9067-09c19aedb078.png)

## Map Nodes to Appliances

1. Drag appliances from the left sidebar to workspace according to the figure and table below.
2. [Rename nodes](#rename-a-node) according to the image below.

![image](../../assets/Base%20Network.png)

| Nodes | Appliances |
| --- | --- |
| PC1 - 4 | VPCS |
| OpenSwitch-Acc-I & II | OPX OpenSwitch 0.4.0 |
| vEOS-Dis-I & II | Arista vEOS 4.29.2F |
| vIOS-Core-I & II, vIOS-Ser-I, vIOS-DMZ-I | Cisco IOSvL2 15.2(20200924:215240) |
| ASAv-I, ASAv-DMZ-I | Cisco ASAv 9.19.1 |
| vIOS-Edge-I | Cisco IOSv 15.9(3)M2 |
| ISP-I & II | Cisco 7200 153-3.XB12 |
| NAT1 & 2 | NAT |

### Rename a Node

- The node must be powered off.
- Double click the node to show the config window. Edit the `Name` of the node and click `Apply` and then `OK`.

![image](https://user-images.githubusercontent.com/69375071/210249045-e5e70657-af20-414f-89ce-3a11b2bf9a28.png)

### Configure Network Adapters

You may want to change the number of adapters in a node. However, our generic base network and ICS network do not need this.

- The node must be powered off and unlinked.
- Double click the device to show the config window. Navigate to `Network` section and change the number of `Adapters`, and then `Apply` and `OK`.

![image](https://user-images.githubusercontent.com/69375071/210222657-c5b86044-892c-4361-bd01-bb292984a7bb.png)

### Configure Cloud Nodes

- Open a SSH terminal for the host where GNS3 server runs.
- Use `ip a` to check all available NICs. Note the `state UP` of `enp5s0` and its IPv4 address `inet 192.168.0.2/24`, which means the NIC `enp5s0` has valid Internet connection.
- Also, note the `state DOWN` of `wlo1` and its empty address, which means the NIC `wlo1` is not connected to the Internet.

> The condition varies among different hosts, but the naming convention remains the same. Pay attention to NICs with names `ens?`, `enp?s0`, `wlo?`, `wlp?s0` and so on.

> You can ignore the localhost `lo` and docker-created NICs `virbr0` and `docker0`.

![image](https://user-images.githubusercontent.com/69375071/211830581-df3713bd-9f7a-4ec9-a21e-3d53e3891d1a.png)


- Select an interface with access to the Internet when connecting `Cloud` devices in GNS3. In this example, select `enp5s0` instead of `wlo1`.

![image](https://user-images.githubusercontent.com/69375071/211830746-93452061-241f-418a-9aa3-fb83e0048ce5.png)

## Connect Nodes

- Click `Add a link` from sidebar to enter linking mode.
- Click on a device and select an interface for one side, and click on another device and select an interface for another side. This forms a connection between them.
- Right click on a wrong connection to delete it.

|||
|-|-|
|![image](https://user-images.githubusercontent.com/69375071/210214930-29754228-799d-4b3f-82c0-2c9ed049e078.png)|![image](https://user-images.githubusercontent.com/69375071/210214939-7ba60df6-5270-4294-9799-7b48161fa01b.png)|

- Follow the image below to connect all devices. It's very simple and straightforward.
> Notice that some devices have port `mgmt` which is an alternative name for eth0 / e0. That's because port 0 is usually used as management port.

![image](../../assets/Base%20Network.png)

## Configure Nodes

- Core
  - End Devices: [PC1 - 4](./PC.md)
  - Access Layer: [OpenSwitch-Acc-I & II](./OpenSwitch-Acc.md)
  - Distribution Layer: [vEOS-Dis-I & II](./vEOS-Dis.md)
  - Core Layer: [vIOS-Core-I & II](./vIOS-Core.md)
- Firewall:
  - [ASAv-I](./ASAv-I.md) (unlicenced ASAv has limited throughput)
  - [USG6000v](./USG6000v.md) (the USG6000v)
- Data Center:
  - [vIOS-Ser-I](./vIOS-Ser-I.md)
  - [Server1](./Server1.md) (this is not used in our network)
- Edge Router: [vIOS-Edge-I](./vIOS-Edge-I.md)
- ISP: [ISP1 & 2](./ISP.md)
- DMZ:
  - [ASAv-DMZ-I](./ASAv-DMZ-I.md)
  - [vIOS-DMZ-I](./vIOS-DMZ-I.md)
  - [Serv-DMZ-I](./Serv-DMZ-I.md)
- ICS:
  - KaliLinux
  - Open vSwitch
  - ONOS (how to add app component in ONOS)
