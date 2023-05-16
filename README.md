# Attack \& Defence in ICS Network

> This repository is under development ⛏ and maintained by [Ziyao Wang](ziyao.wang@se19.qmul.ac.uk), [Mikel Dean](mdeanoses@ceit.es), and [Santiago Figueroa](sfigueroa@ceit.es) as part of the project: *Adaptive Countermeasure based on Software-Defined Networking for Industrial Control Systems*.
>
>> Check the [meeting minutes](./minute/) ✏.

- [Attack \& Defence in ICS Network](#attack--defence-in-ics-network)
	- [ICS Network](#ics-network)
	- [Modbus/TCP](#modbustcp)
	- [HTTP](#http)
	- [Demonstrations](#demonstrations)
	- [Publications](#publications)
	- [TODO](#todo)
	- [DOCS TODO (temporary)](#docs-todo-temporary)

In this project, we practise **SDN**-based Attack & Defence on **Modbus/TCP and HTTP protocols** in an **ICS network**.

<br/>

<details>
<summary>What is an <b>ICS (Industrial Control System) network</b>?</summary>

---

<br/>

![image](./assets/ICS%20Concept.png)

<p align="center"><b>Figure 1</b> OT Components</p>

<br/>

Figure 1 shows the relationships among some ICS-related concepts.

- **OT** (Operational Technology) refers to any programmable system that can monitor and control devices, processes, and events.
- **ICS** (Industrial Control System) is an OT system used to control industrial processes such as manufacturing, production, and distribution.
- **SCADA** (Supervisory Control and Data Acquisition) is a type of ICS with HMI (Human Machine Interface) to monitor and control devices over long distances.
- **PLC** (Programmable Logic Controller) is another type of ICS with programmable memory that stores instructions to control industrial processes locally.

<br/>

![image](https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/media/how-to-set-up-your-network/purdue-model.png)

<p align="center"><b>Figure 2</b> <a href="https://learn.microsoft.com/en-us/azure/defender-for-iot/organizations/best-practices/understand-network-architecture/">Purdue Model for ICS Security</a></p>

<br/>

Figure 2 shows a typical network topology of an industrial organisation. In the topology, the ICS network spreads across level 0, 1, and 2 of the Purdue model.

- **PLC** is often located in the factory. In the ICS network, the **PLC** in level 1 locally controls the sensors and actuators in level 0.
- **SCADA** is often located in the office. In the ICS network, the **HMI** (implements **SCADA**) in level 2 remotely monitors and controls the **PLC** in level 1.

In Figure 2, Microsoft presents its Azure solution to defend the ICS network using several managed switches in level 2 and a remote Microsoft defender. The remote Microsoft defender can interact with these managed switches to monitor and control flow of traffic in the ICS network, which resembles our SDN-based defence.

- Our SDN-based defence uses multiple **Open vSwitch**es to replace the managed switches in level 2, and uses an **ONOS** SDN controller to replace the remote Microsoft defender. The ONOS SDN controller can interact with these Open vSwitches to monitor and control flow of traffic in the ICS network.

Our ICS network is deployed in [GNS3 server](https://github.com/GNS3/gns3-server/), based on a [generic base network](https://brezular.com/2017/09/07/enterprise-network-on-gns3-part-1-introduction/).

---

</details>

<br/>

<details>
<summary>How do we attack the <b>Modbus/TCP and HTTP protocols</b> in the ICS network?</summary>

---

<br/>

[Modbus/TCP](https://modbus.org/docs/Modbus_Messaging_Implementation_Guide_V1_0b.pdf) is a type of Modbus protocol based on TCP (Transmission Control Protocol) that runs on Ethernet and uses TCP port `502`.

- Modbus is a Client-Server protocol designed for communications between different ICSs. The Modbus server runs on TCP port `502`. Modbus protocol has 3 implementations: Modbus/RTU, Modbus/ASCII, and Modbus/TCP. The first two of them run on serial, while the Modbus/TCP runs on Ethernet. As mentioned above, PLC and SCADA are 2 types of ICSs. Modbus/TCP protocol is mainly used for PLC and SCADA to communicate over Ethernet. In this case, PLC is Modbus server and SCADA is Modbus client.

HTTP protocol is widely known and its description can be easily found on the Internet.

- SCADA has a HMI in form of a web UI. Thereby, SCADA can be considered a HTTP server.

We use the [endpoint DoS (Denial of Service)](https://attack.mitre.org/techniques/T1499/) attack on Modbus/TCP and HTTP protocols.

- We use [injection attack on Modbus/TCP](https://github.com/thiagoralves/defcon26/) to cause DoS on PLC (the Modbus server).
- We use [Slowloris attack on HTTP](https://github.com/gkbrk/slowloris/) to cause DoS on SCADA (the HTTP server).

Our attack tools are deployed in a **Kali Linux**, a Debian-based Linux distro with common attack tools bundled. In the case, the Kali Linux can be considered as the attacker.

---

</details>

<br/>

<details>
<summary>How do we use <b>SDN (Software-Defined Networking)</b> to defend the ICS network against the attack?</summary>

---

<br/>

![image](./assets/SDN%20Architecture.png)

<p align="center"><b>Figure 3</b> <a href="https://opennetworking.org/wp-content/uploads/2013/02/TR_SDN_ARCH_1.0_06062014.pdf">SDN Architecture</a></p>

<br/>

**SDN** (Software-Defined Networking) uses software to control the connectivity of network and the flow of traffic, along with inspection and modification of the traffic.

SDN has 3 layers (planes), as shown in Figure 3.

- The data plane contains network devices such as routers and switches. The ICS network is in the data plane.
- The controller plane sits the SDN controller that monitors and controls the data plane via the southbound interface. OpenFlow is a well-known southbound protocol.
- The application plane is for applications to monitor and control the SDN controller via the northbound interface.

Our SDN-based defence deploys multiple **Open vSwitch**es in the data plane, and deploys an **ONOS SDN controller** in the controller plane to monitor and control the ICS network (the data plane) via these Open vSwitches.

- The ONOS SDN controller can interact with Open vSwitches via [OpenFlow protocol](https://opennetworking.org/wp-content/uploads/2014/10/openflow-spec-v1.3.0.pdf). In the case, we can consider these Open vSwitches as "SDN clients" and the ONOS SDN controller as "SDN server".
- As mentioned above in Figure 2, our SDN-based defence resembles Microsoft's Azure solution to the ICS network security.

---

</details>

<br/>

![image](./assets/ICS%20Topology.png)

<p align="center"><b>Figure 4</b> ICS Network Topology</p>

<br/>

Figure 4 is our ICS network topology where Attack & Defence takes place.

- [OpenPLC](https://github.com/thiagoralves/OpenPLC_v3/) (implements PLC) (the Modbus server)
- [Scada-LTS](https://github.com/SCADA-LTS/Scada-LTS/) (implements SCADA) (the Modbus client) (the HTTP server)
- [KaliLinux](https://www.kali.org/) (implements attacker)
- [Open vSwitch](https://www.openvswitch.org/) (implements "SDN client")
- [ONOS SDN controller](https://wiki.onosproject.org/) (implements "SDN server")

## ICS Network

1. [Install GNS3 client and server](./network/gns3/README.md)
2. [Deploy generic base network in GNS3 server](./network/base/README.md)
3. [Deploy ICS network in GNS3 server](./network/ics/README.md)

## Modbus/TCP

1. [Conduct the injection attack on Modbus/TCP](./modbus/attack/README.md)
2. [Deploy countermeasure in ONOS against Modbus/TCP DoS attack](./modbus/defence/README.md)

## HTTP

1. [Conduct the Slowloris attack on HTTP](./http/attack/README.md)
2. [Deploy countermeasure in ONOS against slow HTTP attack](./http/defence/README.md)

## Demonstrations

- [Check the video demonstration of Modbus/TCP DoS attack](https://youtu.be/zfWXR3ZAG7I)
- videos to be added
- PowerPoint slides to be added

## Publications

- Medium blog link to be added

## TODO

- Change the ASAv firewall to pfsense/opnsense or find a licenced firewall
- Include the [level-0 components](https://github.com/sfl0r3nz05/ICSsVirtualForCiberSec/) of the Purdue model 
- Attack and defence on OSPF
- Use other forms of attack different from the [endpoint DoS](https://attack.mitre.org/techniques/T1499/)

## DOCS TODO (temporary)

- [detailed explaination](./network/ics/README.md)
- [configuration of OpenPLC, Scada-LTS, KaliLinux, Open vSwitch, ONOS, USG6000v](./network/nodes/configuration/README.md)
- [how to compile and run Modbus attacker](./modbus/attack/README.md)
- [how to use IntelliJ IDEA](./modbus/defence/README.md)
- [how to install and run slowhttptest](./http/attack/README.md)
- [demonstration](#demonstrations)
- [publication](#publications)
