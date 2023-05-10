# Attack \& Defence in ICS Network

> This repository is under development ⛏ and maintained by [Ziyao Wang](ziyao.wang@se19.qmul.ac.uk), [Mikel Dean](mdeanoses@ceit.es), and [Santiago Figueroa](sfigueroa@ceit.es) as part of the project: *Adaptive Countermeasure based on Software-Defined Networking for Industrial Control Systems*.
>
>> Check the [meeting minutes](./Minutes/) ✏.

- [Attack \& Defence in ICS Network](#attack--defence-in-ics-network)
	- [ICS Network](#ics-network)
	- [Modbus/TCP](#modbustcp)
	- [HTTP](#http)
	- [OSPF](#ospf)
	- [TODO](#todo)

This project demonstrates Attack & Defence in an ICS (Industrial Control System) network.

- Attack & Defence is conducted on Modbus/TCP protocol and HTTP protocol.
- The attack is a type of [endpoint Denial of Service](https://attack.mitre.org/techniques/T1499/) attack, and the attack tools are [injection on Modbus/TCP](https://github.com/thiagoralves/defcon26/) and [Slowloris on HTTP](https://github.com/gkbrk/slowloris/).
- The defence is based on SDN (Software-Defined Networking), and the countermeasure is deployed on an [ONOS SDN controller](https://wiki.onosproject.org/).

![image](https://user-images.githubusercontent.com/69375071/236771833-c99033d5-a1b3-4bc2-8c9b-ee2e22c74495.png)

The figure shows the relationships among some ICS-related concepts.

- **OT** (Operational Technology) refers to any programmable system that can monitor and control devices, processes, and events.
- **ICS** (Industrial Control System) is an OT system used to control industrial processes such as manufacturing, production, and distribution.
- **SCADA** (Supervisory Control and Data Acquisition) is a type of ICS with HMI (Human Machine Interface) to monitor and control devices over long distances.
  - SCADA is often found in office.
- **PLC** (Programmable Logic Controller) is a type of ICS with programmable memory that stores instructions to control industrial processes locally.
  - PLC is often found in factory.

![image](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/media/how-to-set-up-your-network/purdue-model.png)

The figure is the [Purdue model for ICS security](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/best-practices/understand-network-architecture/). A typical ICS spreads across level 0, 1, and 2.
- The level-1 **PLC** controls the level-0 sensors and actuators locally.
- The level-2 **HMI** implements SCADA to monitor and control the level-1 PLC remotely.

## ICS Network

We deployed an experimental ICS network to conduct attack & defense. In our practice, we use [OpenPLC](https://github.com/thiagoralves/OpenPLC_v3) to implement the PLC, and [Scada-LTS](https://github.com/SCADA-LTS/Scada-LTS) to implement the SCADA Human Machine Interface (HMI).

Our experimental ICS network can be considered to sit between level 1 and level 2 of the OT network in Purdue reference model, while preserving some kind of portability.

Scada-LTS exchanges Modbus/TCP Application Data Units (ADUs) with OpenPLC to monitor and control OpenPLC. Injection attack is performed by directly flooding Modus/TCP ADUs that can manually turn the heater on to OpenPLC.

OSPFv2 is applied by routers and layer-3 switches to route within the experimental ICS network. Disguised LSA attack is performed by first triggering the fightback mechanism of the victim router and then sending malicious LSA disguised as its fightback LSA to the target router.

Open vSwitch is deployed and connected to ONOS SDN controller to control the network plane. ......

OpenPLC and Scada-LTS

Connecting Scada-LTS to OpenPLC

Open vSwitch

ONOS SDN controller

## Modbus/TCP

[Conduct and examine the Modbus attack](./4.Modbus/README.md).

[Check the video demonstration on YouTube](https://youtu.be/zfWXR3ZAG7I).

Deploy countermeasure.

## HTTP

[Conduct and examine the OSPF attack](./5.OSPF/README.md).

Deploy countermeasure.

## OSPF

attacker not implemented

## TODO

`//TODO recheck hyperlinks in markdown files`