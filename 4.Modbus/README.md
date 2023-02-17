# Modbus Attack

In this article, we introduce the Modbus protocol, discuss the weakness of Modbus/TCP protocol, and conduct an attack on OpenPLC via Modbus/TCP protocol.

## Modbus Protocol

Modbus is a simple Master-Slave protocol designed for communications between Programmable Logic Controllers (PLC) and Human Machine Interfaces (HMI) within Industrial Control Systems (ICS). Modbus operates on data link layer (layer 2) and is independent of physical layer (layer 1), so it can be deployed on both Serial and Ethernet.

The Application Data Unit (ADU) of Modbus has 3 implementations: Modbus/RTU, Modbus/ASCII, and Modbus/TCP. The first two protocols operate on Serial, while Modbus/TCP runs on Ethernet. The picture below shows the ADU of Modbus/RTU protocol. In this project, we use Modbus/TCP for OpenPLC and ScadaLTS to communicate over Ethernet.

![image](https://user-images.githubusercontent.com/69375071/219570333-4141b14c-51bc-4796-ac57-31c9f6b4512a.png)

The function code is of 1 Byte with a range from 1 (00000001) to 255 (11111111). Some of the function codes of Modbus protocol are pre-defined (well-known). For example, 1 means `Read Coils`, 3 means `Read Holding Registers`, and 6 means `Write Single Register`.

The data is defined by the OpenPLC itself. For the same function code 6 `Write Single Register`, specified data tells OpenPLC to write to specified register with specified value.

The Application Data Unit (ADU) of Modbus/TCP protocol doesn't contain the slave (serial) address or LRC/CRC error check, for they are already implemented in TCP/IP protocol (IP address and TCP checksum). Besides, Modbus/TCP transfer is in plaintext, which is vulnerable to interception, modification, and fabrication. Thereby, an attacker can easily attack an OpenPLC provided that he is in the same intranet with the OpenPLC and knows the IP address of that OpenPLC.

## Attack on OpenPLC via Modbus/TCP

We first capture the ADU packets that go in and out the OpenPLC using Wireshark. This is to examine the different data defined by the OpenPLC for different purposes. For function code 6 `Write Single Register`, we see that reference number 4 data 0001 means set `mode_register` to `manual`, and reference number 5 data 0001 means manually power on the `heater`. So, if we continue to send [6 4 0001] and then [6 5 0001] to OpenPLC, then the OpenPLC should always be in manually on mode. This is a type of injection attack.

| | |
|-|-|
| ![image](https://user-images.githubusercontent.com/69375071/219570391-eb2c66d8-5a49-41b5-bb71-a388eff1b283.png) | ![image](https://user-images.githubusercontent.com/69375071/219570528-ca1da4cc-85a7-48ac-8ae7-caeeaee40fd4.png) |

The picture below shows the successful injection attack.

![image](https://user-images.githubusercontent.com/69375071/219570621-9ecb20a5-be76-4d0f-b608-f2fd7706a733.png)
