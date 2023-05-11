# Deploy Generic Base Network in GNS3 Server

Portable project of the generic base network is available on [Google Drive](https://drive.google.com/drive/folders/1ooRXrQyf2Lz0mGtcHeRZjTrhDW_SKV4w/).

## Generic Base Network

![image](https://user-images.githubusercontent.com/69375071/210197317-12a7553f-9dea-4a2c-9336-2f2b721b06b8.png)

1. This network consists of 3 layers: *access*, *distribution* and *core*.
2. The *data center (DC)* is composed of the layer 3 Cisco switch and the server. The design of the DC is very simplified as the network tiers are squeezed to a single switch layer 3 switch. The aim is to show configuration of the services running on the Server1 instead of discussing the complete DC design.
3. The company *edge router* is connected to the Internet using two *Internet Service Providers (ISPs)*.
4. The *Cisco ASA firewall* connects a campus network, data Center and the *edge router*.
5. The *edge router* connected *DMZ* to the rest of the enterprise network and to the Internet. The DMZ consists of the *Cisco ASA firewall*, layer 3 Cisco switch and the *DMZ* server.
6. The enterprise is connected to the ISP1 and ISP2 routers via enterprise *edge router*. Both *ISP* routers  are bridged via *GNS3 clouds* to the server Ethernet Card in order to simulate connection to the Internet.

## Import GNS3 Appliances

- Follow [this document](./Appliances/README.md) to import all required appliances to GNS3 server.

## Deploy Generic Base Network

- Follow [this document](./Deployment/README.md) to map nodes to appliances and connect all nodes.

## Configure Nodes

- Core
  - End Devices: [PC1 - 4](./Configuration/PC.md)
  - Access Layer: [OpenSwitch-Acc-I & II](./Configuration/OpenSwitch-Acc.md)
  - Distribution Layer: [vEOS-Dis-I & II](./Configuration/vEOS-Dis.md)
  - Core Layer: [vIOS-Core-I & II](./Configuration/vIOS-Core.md)
- Firewall: [ASAv-I](./Configuration/ASAv-I.md)
- Data Center:
  - [vIOS-Ser-I](./Configuration/vIOS-Ser-I.md)
  - [Server1](./Configuration/Server1.md)
- Edge Router: [vIOS-Edge-I](./Configuration/vIOS-Edge-I.md)
- ISP: [ISP1 & 2](./Configuration/ISP.md)
- DMZ:
  - [ASAv-DMZ-I](./Configuration/ASAv-DMZ-I.md)
  - [vIOS-DMZ-I](./Configuration/vIOS-DMZ-I.md)
  - [Serv-DMZ-I](./Configuration/Serv-DMZ-I.md)
