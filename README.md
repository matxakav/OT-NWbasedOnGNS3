# Attack \& Defence in ICS Network

> This repository is under development ⛏ and maintained by [Ziyao Wang](ziyao.wang@se19.qmul.ac.uk), [Mikel Dean](mdeanoses@ceit.es), and [Santiago Figueroa](sfigueroa@ceit.es) as part of the project: *Adaptive Countermeasure based on Software-Defined Networking for Industrial Control Systems*.
>
>> Check the [meeting minutes](/minute/) ✏.

---

- [Attack \& Defence in ICS Network](#attack--defence-in-ics-network)
	- [ICS Network](#ics-network)
	- [Modbus/TCP](#modbustcp)
	- [HTTP](#http)

---

This project shows Attack & Defence in an ICS (Industrial Control System) network.

- The ICS network is deployed in [GNS3 server](https://github.com/GNS3/gns3-server/), based on a [generic base network](https://brezular.com/2017/09/07/enterprise-network-on-gns3-part-1-introduction/).
- Attack & Defence is conducted on [Modbus/TCP protocol](https://modbus.org/docs/Modbus_Messaging_Implementation_Guide_V1_0b.pdf) and HTTP protocol.
- The attack is a type of [endpoint DoS (Denial of Service)](https://attack.mitre.org/techniques/T1499/) attack, and the attack tools are [injection on Modbus/TCP](https://github.com/thiagoralves/defcon26/) and [Slowloris on HTTP](https://github.com/gkbrk/slowloris/).
- The defence is based on SDN (Software-Defined Networking), and the countermeasure is deployed on an [ONOS SDN controller](https://wiki.onosproject.org/) to control multiple [Open vSwitches](https://www.openvswitch.org/).

![image](/assets/ICS%20Concept.png)

The figure shows the relationships among some ICS-related concepts.

- **OT** (Operational Technology) refers to any programmable system that can monitor and control devices, processes, and events.
- **ICS** (Industrial Control System) is an OT system used to control industrial processes such as manufacturing, production, and distribution.
- **SCADA** (Supervisory Control and Data Acquisition) is a type of ICS with HMI (Human Machine Interface) to monitor and control devices over long distances.
- **PLC** (Programmable Logic Controller) is a type of ICS with programmable memory that stores instructions to control industrial processes locally.

![image](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/media/how-to-set-up-your-network/purdue-model.png)

The figure is [Purdue model for ICS security](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/best-practices/understand-network-architecture/). A typical ICS spreads across level 0, 1, and 2.
- **PLC** is often found in factory. The **PLC** in level 1 locally controls the sensors and actuators in level 0.
- **SCADA** is often found in office. The **HMI** (implements **SCADA**) in level 2 remotely monitors and controls the **PLC** in level 1.

![image](/assets/ICS%20Topology.png)

The figure is an ICS network topology where Attack & Defence takes place.

- [OpenPLC](https://github.com/thiagoralves/OpenPLC_v3/) (implements PLC)
- [Scada-LTS](https://github.com/SCADA-LTS/Scada-LTS/) (implements SCADA)
- [KaliLinux](https://www.kali.org/) (implements attacker)
- [Open vSwitch](https://www.openvswitch.org/) (implements SDN "client")
- [ONOS SDN controller](https://wiki.onosproject.org/) (implements SDN "server")

---

## ICS Network

1. [Install GNS3 client and server](/network/gns3/README.md)
2. [Deploy generic base network in GNS3 server](/network/base/README.md)
3. [Deploy ICS network in GNS3 server](/network/ics/README.md)

## Modbus/TCP

1. [Conduct the injection attack on Modbus/TCP](/modbus/attack/README.md)
2. [Deploy countermeasure in ONOS against Modbus/TCP DoS attack](/modbus/defence/README.md)

## HTTP

1. [Conduct the Slowloris attack on HTTP](/http/attack/README.md)
2. [Deploy countermeasure in ONOS against slow HTTP attack](/http/defence/README.md)
