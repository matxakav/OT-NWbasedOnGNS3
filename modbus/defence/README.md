# Countermeasure against Injection on Modbus/TCP

The countermeasure is an app component in ONOS SDN controller.

The countermeasure is based on the design of injection attack itself. From Wireshark captures, we find Modbus/TCP packets sent by the attacker is always with function code 6 (WRITE_SINGLE_REGISTER). This separates the attack traffic from normal query traffic of Scada-LTS.

```c
//Modbus TCP packet {  TransID |   ProtID  |   MsgLen  | UID |  FC |        Data           }  
char packet1[] =    {0x00, 0x00, 0x00, 0x00, 0x00, 0x06, 0x01, 0x06, 0x00, 0x04, 0x00, 0x01};
char packet2[] =    {0x00, 0x00, 0x00, 0x00, 0x00, 0x06, 0x01, 0x06, 0x00, 0x05, 0x00, 0x01};
```

The code from injection attack defines 2 packets to attack OpenPLC via Modbus/TCP. `packet1` is used to set OpenPLC to manual mode, and `packet2` is used to set OpenPLC heater to on.

![image](/assets/ONOS%20Modbus.svg)

The figure shows the mechanism of SDN-based countermeasure against Modbus/TCP.

- `PacketProcessor` monitors all traffic through Open vSwitches and selects Modbus/TCP packets with function code 6.
- `FreqAnalyzer` periodically analyses this packets to detect DoS attack based on packet frequency. In 1s, if there are more than 10 Modbus write packets from the same source IP, then the IP is considered an attacker and defence flow entries are deployed in all Open vSwitches to block it.

Refer to [this repo](https://github.com/wangziyao318/onos-app-ics/) for source code of the countermeasure.

- The countermeasure app is written in Java.
- Use OpenJDK11 + Maven in IntelliJ IDEA.