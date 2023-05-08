# Attack \& Defence in ICS[^1] Network

> This repository is under development ⛏ and maintained by [Ziyao Wang](ziyao.wang@se19.qmul.ac.uk), [Mikel Dean](mdeanoses@ceit.es), and [Santiago Figueroa](sfigueroa@ceit.es) as part of the project: *Adaptive Countermeasure based on Software-Defined Networking for Industrial Control Systems*.
>
>> Check the [meeting minutes](./Minutes/) ✏.

`//TODO recheck hyperlinks in markdown files`

## Introduction

- This repository demonstrates Attack & Defence in an ICS[^1] network.
- Attack & Defence is conducted on Modbus/TCP[^2] protocol and HTTP protocol.
- The attack tools we use are [injection attack on Modbus/TCP](https://github.com/thiagoralves/defcon26) and [Slowloris attack on HTTP](https://github.com/gkbrk/slowloris).
- The countermeasure is implemented with SDN[^3].

For developers, this project demonstrates two countermeasures implemented by [ONOS SDN controller](https://wiki.onosproject.org) against [injection attack on Modbus/TCP](https://github.com/thiagoralves/defcon26) and [disguised LSA attack on OSPF](https://github.com/lizitong67/OSPF_Attack_and_Detection) for a small part of a simulated Industrial Control System.

### OT, ICS, SCADA, PLC, Modbus, OSPF Definitions

Refer to [this document](./Docs/OT_ICS_SCADA_PLC.md).

### Experimental ICS Network

We deployed an experimental ICS network to conduct attack & defense. In our practice, we use [OpenPLC](https://github.com/thiagoralves/OpenPLC_v3) to implement the PLC, and [Scada-LTS](https://github.com/SCADA-LTS/Scada-LTS) to implement the SCADA Human Machine Interface (HMI).

![image](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/media/how-to-set-up-your-network/purdue-model.png)

Our experimental ICS network can be considered to sit between level 1 and level 2 of the OT network in Purdue reference model, while preserving some kind of portability.

Scada-LTS exchanges Modbus/TCP Application Data Units (ADUs) with OpenPLC to monitor and control OpenPLC. Injection attack is performed by directly flooding Modus/TCP ADUs that can manually turn the heater on to OpenPLC.

OSPFv2 is applied by routers and layer-3 switches to route within the experimental ICS network. Disguised LSA attack is performed by first triggering the fightback mechanism of the victim router and then sending malicious LSA disguised as its fightback LSA to the target router.

Open vSwitch is deployed and connected to ONOS SDN controller to control the network plane. ......

`//TODO add description of adaptive countermeasure`

The experimental ICS network is IPv4 only.

## Deployment of GNS3 Server on EC2 Instance

This repo requires the deployment of 2 AWS EC2 instances:

The first instance hosts the GNS3 server running the experimental IT-Network/OT-Network.
- [Deploy the GNS3 server](./0.GNS3/README.md) on an EC2 instance.

Second hosts the SDN controller.

## Deployment of Experimental ICS Network on GNS3 Server

`//TODO merge the docs`

[Deploy the Experimental IT Network](./1.IT-Network/README.md) on the GNS3 Server.

[Deploy the Experimental OT Network](./2.OT-Network/README.md) on the GNS3 Server.

## Deployment of SDN Controller on EC2 Instance

[Deploy the SDN-Controller](./3.SDN/README.md) connected to the Experimental Base Network.

## Modbus Attack

[Conduct and examine the Modbus attack](./4.Modbus/README.md).

[Check the video demonstration on YouTube](https://youtu.be/zfWXR3ZAG7I).

## OSPF Attack

[Conduct and examine the OSPF attack](./5.OSPF/README.md).

## Adaptive Countermeasure

TBA

## Troubleshooting

[Examines the main troubleshooting](./Troubleshootings/README.md)

[^1]: ICS = Industrial Control System.

[^2]: TCP = Transmission Control Protocol.

[^3]: SDN = Software-Defined Networking.