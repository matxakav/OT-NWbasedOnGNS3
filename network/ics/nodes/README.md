# Instantiate and Configure Nodes

- [Instantiate and Configure Nodes](#instantiate-and-configure-nodes)
	- [Instantiate Nodes](#instantiate-nodes)
		- [Rename a Node](#rename-a-node)
		- [Configure Network Adapters](#configure-network-adapters)
		- [Configure a Cloud Node](#configure-a-cloud-node)
		- [Change Number of Network Adapters](#change-number-of-network-adapters)
		- [Link Nodes](#link-nodes)
	- [Configure Nodes](#configure-nodes)

The word "instantiate" is a Java-style expression to `new` an object from a given class. In GNS3 server, it describes the action to create a node from a given appliance.

## Instantiate Nodes

We map nodes to appliances in the generic base network.

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

![image](../../assets/Base%20Network.png)

1. Instantiate nodes by dragging appliances from the left sidebar to workspace according to the figure and table above.
2. [Rename these nodes](#rename-a-node) according to the figure above.
3. [Configure network adapters](#configure-network-adapters) for PC1 - PC4.
4. [Link these nodes](#link-nodes) according to the figure above.

### Rename a Node

This applies to all nodes in the generic base network and ICS network. The name of each node in the same workspace is unique.

- The node must be powered off.
- Double click the node to show the config window. Edit the `Name` of the node and click `Apply` and then `OK`.

![image](https://user-images.githubusercontent.com/69375071/210249045-e5e70657-af20-414f-89ce-3a11b2bf9a28.png)

### Configure Network Adapters

This applies to PC1 ~ PC4, OpenPLC1, Scada-LTS1, KaliLinux1 ~ KaliLinux2, and OVS-I ~ OVS-IV. In brief, all VPCS nodes and Docker containers need configuring.

PC1 ~ PC4

```
ip 192.168.10.1/24 192.168.10.254
```

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/210246729-e35e1168-86da-4eee-aafc-9ce40456257e.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/210246751-4edf4d3d-dfff-4966-8303-87e800c80b70.png" width="49%" />

</div>

OpenPLC1, Scada-LTS1, KaliLinux1 ~ KaliLinux2, and OVS-I ~ OVS-IV

`//TODO picture`

### Configure a Cloud Node

This applies to Cloud1 ~ Cloud4 in the ICS network.

- Select an interface with access to the Internet when connecting `Cloud` devices in GNS3. In this example, select `docker0` NIC.

![image](https://user-images.githubusercontent.com/69375071/211830746-93452061-241f-418a-9aa3-fb83e0048ce5.png)

### Change Number of Network Adapters

You may want to change the number of network adapters in a node. However, our generic base network and ICS network do not need this.

- The node must be powered off and unlinked.
- Double click the device to show the config window. Navigate to `Network` section and change the number of `Adapters`, and then `Apply` and `OK`.

![image](https://user-images.githubusercontent.com/69375071/210222657-c5b86044-892c-4361-bd01-bb292984a7bb.png)

### Link Nodes

- Click `Add a link` from the left toolbar to enter linking mode.
- Click on a node and select an adapter for one side, and click on another node and select an adapter for another side. This creates a link between them.
- Right click on a wrong link to delete it.

<div align=center>

<img src="https://user-images.githubusercontent.com/69375071/210214930-29754228-799d-4b3f-82c0-2c9ed049e078.png" width="49%" />
<img src="https://user-images.githubusercontent.com/69375071/210214939-7ba60df6-5270-4294-9799-7b48161fa01b.png" width="49%" />

</div>

<br/>

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
