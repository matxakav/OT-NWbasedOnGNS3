# Instantiate and Configure Nodes

- [Instantiate and Configure Nodes](#instantiate-and-configure-nodes)
	- [Instantiate Nodes](#instantiate-nodes)
		- [Rename a Node](#rename-a-node)
		- [Configure Network Adapters](#configure-network-adapters)
		- [Link Nodes](#link-nodes)
	- [Configure Nodes](#configure-nodes)

The word "instantiate" is a Java-style expression to `new` an object from a given class. In GNS3 server, it describes the action to create a node from a given appliance.

## Instantiate Nodes

We map nodes to appliances in the generic base network.

- VPCS, the Virtual PC Simulator, is a prebuilt appliance in GNS3 server.

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

![image](../../../assets/Base%20Network.png)

1. Instantiate nodes by dragging appliances from the left sidebar to workspace according to the figure and table above.
2. [Rename these nodes](#rename-a-node) according to the figure above.
3. [Configure network adapters](#configure-network-adapters) for PC1 - PC4.
4. [Link these nodes](#link-nodes) according to the figure above.

### Rename a Node

The name of each node in the same workspace is unique.

- The node must be powered off.
- Double click the node to show the config window. Edit the `Name` of the node and click `Apply` and then `OK`.

![image](../../../assets/Rename%20a%20Node.png)

### Configure Network Adapters

This applies to PC1 ~ PC4 in the generic base network. In principle, all VPCS nodes and Docker containers need configuring.

These nodes should be powered off before configuring. For each VPCS node in PC1 ~ PC4, right click on the node and select `Edit config` to open the `startup.vpc` config file. We want to append some lines into the file and then `Save` our configuration.

<div align=center>

<img src="../../../assets/Edit%20Config.png" width="41%" />
<img src="../../../assets/PC%20Config.png" width="58%" />

</div>

<br/>

- PC1

```
set pcname PC1
ip 192.168.10.1/24 192.168.10.254
ip dns 8.8.8.8
```

- PC2

```
set pcname PC2
ip 192.168.40.1/24 192.168.40.254
ip dns 8.8.8.8
```

- PC3

```
set pcname PC3
ip 172.16.50.1/24 172.16.50.254
ip dns 8.8.8.8
```

- PC4

```
set pcname PC4
ip 195.1.1.161/29 195.1.1.166
ip dns 8.8.8.8
```

### Link Nodes

- Click the `Add a link` button from the left toolbar (you may hover on each button to see its description) to enter the linking mode.
  - In the linking mode, click on a node and select an adapter for one side, and click on another node and select an adapter for another side. This creates a link between them.
- Right click on a wrong link to delete it.

<div align=center>

<img src="../../../assets/Add%20a%20Link.png" width="37%" />
<img src="../../../assets/Delete%20a%20Link.png" width="61%" />

</div>

<br/>

## Configure Nodes

In principle, a node should be started before configuration. It's advised to read these documents one by one, because duplicate steps won't be repeat in detail again.

- Core:
  - Access Layer: [OpenSwitch-Acc-I & II](./OpenSwitch-Acc.md)
  - Distribution Layer: [vEOS-Dis-I & II](./vEOS-Dis.md)
  - Core Layer: [vIOS-Core-I & II](./vIOS-Core.md)
- Firewall:
  - [ASAv-I](./ASAv-I.md) (unlicenced Cisco ASAv firewall with limited throughput)
  - [USG6000v-I](./USG6000v-I.md) (HUAWEI's firewall with a 15-day trial licence and better throughput, an alternative to ASAv-I)
- Data Center: [vIOS-Ser-I](./vIOS-Ser-I.md)
- Edge Router: [vIOS-Edge-I](./vIOS-Edge-I.md)
- ISP: [ISP-I & II](./ISP.md)
- DMZ:
  - [ASAv-DMZ-I](./ASAv-DMZ-I.md)
  - [vIOS-DMZ-I](./vIOS-DMZ-I.md)
