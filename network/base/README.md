# Deploy Generic Base Network in GNS3 Server

- [Deploy Generic Base Network in GNS3 Server](#deploy-generic-base-network-in-gns3-server)
	- [Generic Base Network](#generic-base-network)
	- [Deploy Generic Base Network in GNS3 Server](#deploy-generic-base-network-in-gns3-server-1)

Portable project of the generic base network is available on [Google Drive](https://drive.google.com/drive/folders/1ooRXrQyf2Lz0mGtcHeRZjTrhDW_SKV4w/).

- You can skip this guide by downloading the portable project and open it via GNS3 client.
- Read this guide if you want to customise your generic base network in GNS3 server.

## Generic Base Network

<br/>

![image](../../assets/Generic%20Base%20Network.png)

<p align="center"><b>Figure 1</b> Generic Base Network</p>

<br/>

Figure 1 shows the topology of a generic base network. Refer to [Brezular's blog](https://brezular.com/2017/09/07/enterprise-network-on-gns3-part-1-introduction/) for detailed introductions if you want to dig into it.

<br/>

![image](../../assets/GNS3%20GUI.png)

<p align="center"><b>Figure 2</b> Generic Base Network in GNS3 Server</p>

<br/>

Figure 2 is our generic base network deployed in GNS3 server in our home desktop. The network is visualised and controlled by GNS3 client in our laptop. Here, we define some terminologies.

- An **appliance** is a template of a network device in the left sidebar.
- A **node** is an instantiation of an appliance in the central workspace.
  - Drag an appliance from the left sidebar to the workspace to create a node of its type.
  - The right sidebar displays the status and sockets of all nodes in the workspace.

## Deploy Generic Base Network in GNS3 Server

1. [Import all appliances into GNS3 server](./appliances/README.md)
2. [Instantiate and configure nodes](./nodes/README.md)