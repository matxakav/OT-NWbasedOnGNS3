# Deploy Generic Base Network in GNS3 Server

- [Deploy Generic Base Network in GNS3 Server](#deploy-generic-base-network-in-gns3-server)
	- [Generic Base Network](#generic-base-network)
	- [Appliances](#appliances)
	- [Nodes](#nodes)

Portable project of the generic base network is available on [Google Drive](https://drive.google.com/drive/folders/1ooRXrQyf2Lz0mGtcHeRZjTrhDW_SKV4w/).

- You can skip this guide by downloading the portable project and open it via GNS3 client.
- Read this guide if you want to customise your generic base network in GNS3 server.

## Generic Base Network

![image](/assets/Generic%20Base%20Network.png)

The figure shows the topology of generic base network. Refer to [Brezular's blog](https://brezular.com/2017/09/07/enterprise-network-on-gns3-part-1-introduction/) for detailed introductions.

![image](/assets/GNS3%20GUI.png)

The figure is the generic base network deployed in GNS3 server.

- An **appliance** is a template of a network device in the left column.
- A **node** is an instantiation of an appliance in the central workspace.
  - Drag an appliance in left column to the workspace to create a node of its type.
  - The right column shows the status and sockets of all nodes in the workspace.

## Appliances

1. [Use GNS3 client to import all required appliances into GNS3 server](../appliances/README.md)

## Nodes

1. [Instantiate nodes from appliances](../nodes/README.md)
2. [Configure nodes](../nodes/configuration/README.md)
