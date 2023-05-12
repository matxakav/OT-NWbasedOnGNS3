# Countermeasure against Slowloris on HTTP

The countermeasure is an app component in ONOS SDN controller.

The countermeasure is based on the vulnerability of the Slowloris attack itself.

```python
def slowloris_iteration():
    logging.info("Sending keep-alive headers...")
    logging.info("Socket count: %s", len(list_of_sockets))

    # Try to send a header line to each socket
    for s in list(list_of_sockets):
        try:
            s.send_header("X-a", random.randint(1, 5000))
        except socket.error:
            list_of_sockets.remove(s)
```

The code taken from Slowloris attack is a python function to send keep-alive headers periodically to keep all established connections alive. In the `for` loop, all keep-alive headers are sent one by one. Since modern CPUs are very powerful, all this `send_header()` functions are executed within 1 millisecond. Also, the unit of timestamp is millisecond for a TCP packet. So, this results in a bunch of TCP keep-alive packets with the same timestamp value. That's where our cuontermeasure focuses on.

![image](/assets/ONOS%20HTTP.svg)

The figure shows the mechanism of SDN-based countermeasure against Slowloris on HTTP.

- `PacketProcessor` monitors all traffic through Open vSwitches and selects TCP packets with PSH+ACK flag.
- `FreqAnalyzer` periodically analyses this packets to detect Slowloris attack based on timestamp. In 10s, if there are more than 5 TCP packets with the same timestamp from the same source IP, then the IP is considered an attacker and defence flow entries are deployed in all Open vSwitches to block it.

Refer to [this repo](https://github.com/wangziyao318/onos-app-ics/) for source code of the countermeasure.

- The countermeasure app is written in Java.
- Use OpenJDK11 + Maven in IntelliJ IDEA.