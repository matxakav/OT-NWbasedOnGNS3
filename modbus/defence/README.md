# Countermeasure against Injection on Modbus/TCP

The countermeasure is implemented as an app component in ONOS SDN controller, as shown in Figure 1.

- ONOS is written in pure Java. You should be able to use Java + Maven to programme the app component. If not, watch by yourself tutorials on YouTube or BiliBili on basic usage of Java + Maven in IntelliJ IDEA.
- An app component has listeners to listen for the state change of ONOS and take actions reactively.
  - A DeviceListener can identify newly added devices (Open vSwitches).
  - A PacketProcessor is called whenever ONOS receives a new packet from an Open vSwitch.
- An app component can call methods in services to query and edit ONOS configurations.
  - The methods in FlowObjectiveService and FlowRuleService can add or remove flow entries from Open vSwitches.
  - The methods in CoreService and ComponentConfigService are essential for the app component to be registered and configured upon activation.

<br/>

![image](../../assets/ONOS%20Service.png)

<p align="center"><b>Figure 1</b> ONOS Subsystem Structure</p>

<br/>

The countermeasure is based on the design of injection attack itself. From [Wireshark capture](../../network/ics/README.md#7-capture-the-traffic-using-wireshark), we find Modbus/TCP packets sent by the attacker is always with function code 6 `WRITE_SINGLE_REGISTER`. This separates the attack traffic from the periodical query traffic of Scada-LTS with function code 1 or 3.

```c
//Modbus TCP packet {  TransID |   ProtID  |   MsgLen  | UID |  FC |        Data           }  
char packet1[] =    {0x00, 0x00, 0x00, 0x00, 0x00, 0x06, 0x01, 0x06, 0x00, 0x04, 0x00, 0x01};
char packet2[] =    {0x00, 0x00, 0x00, 0x00, 0x00, 0x06, 0x01, 0x06, 0x00, 0x05, 0x00, 0x01};
```

The code above from injection attack defines 2 packets to attack OpenPLC via Modbus/TCP. `packet1` is used to set OpenPLC to manual mode, and `packet2` is used to set OpenPLC heater to on.

<br/>

![image](../../assets/ONOS%20Modbus.svg)

<p align="center"><b>Figure 2</b> Modbus/TCP Countermeasure Workflow</p>

<br/>

Figure 2 shows the mechanism of SDN-based countermeasure as an app component of ONOS against Modbus/TCP.

- `PacketProcessor` monitors all traffic through Open vSwitches and selects Modbus/TCP packets with function code 6 (aka. Modbus write packets).
- `FreqAnalyzer` periodically analyses this packets to detect DoS attack based on packet frequency. In 1 second, if there are more than 10 Modbus write packets from the same source IP, then the IP is considered an attacker and defence flow entries are deployed in all Open vSwitches to block it.

Refer to [this repo](https://github.com/wangziyao318/onos-app-ics/) for source code of the countermeasure.

We use OpenJDK11 + Maven in IntelliJ IDEA to programme the app component.

- Watch [this video](https://www.youtube.com/watch?v=D1sRK8JLCQ4) or others on YouTube to get started with Maven in IDEA.
- Chinese users watch [this series](https://www.bilibili.com/video/BV1Aa411M7fu/) or other videos on BiliBili to get started with Maven in IDEA.
- Refer to the [official document](https://www.jetbrains.com/help/idea/getting-started.html) on how to use IntelliJ IDEA.

Concerning JDKs, you can easily download them in "Project Structure" in IntelliJ IDEA, as shown in Figure 3. You needn't install it in advance.

The Maven bundled in IDEA is enough for use.

<br/>

![image](../../assets/Project%20Structure.png)

<p align="center"><b>Figure 3</b> Download JDK in IDEA</p>
