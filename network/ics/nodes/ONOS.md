# ONOS

Since ONOS's web UI relies on WebSocket protocol and latest GNS3 server [can't relay WebSocket traffic properly](https://github.com/GNS3/gns3-server/issues/2199), we deploy the ONOS SDN controller directly into the Docker engine in our home desktop.

Create an ONOS container using the following command. It starts a container as system daemon with the name "onos", a web UI listening at "TCP:127.0.0.1:8181" and a SSH console listening at "TCP:127.0.0.1:8101".

- This command only needs to be executed once.

```sh
docker run --restart=unless-stopped -d -p 127.0.0.1:8181:8181 -p 127.0.0.1:8101:8101 --name onos onosproject/onos:2.7.0
```

After that, we can stop the onos container using `docker stop onos` to free about 1GB RAM, and start stopped onos container using `docker start onos`.

We can also list all docker containers using `docker ps -a`, and list only running docker containers using `docker ps`.

We can use `docker network inspect bridge` to find the IPv4 assigned to the running ONOS container.

- The running ONOS container in the figure has IPv4 address `172.17.0.2`.

![image](../../../assets/docker%20network%20inspect%20bridge.png)

After we start the ONOS container, we can access its web UI on http://127.0.0.1:8181/onos/ui/ in our laptop if we start the SSH local forwarding. Login with username/password: onos/rocks and then we enter the "Topology" view.

- In topology view, press the `/` key to show help.

![image](../../../assets/ONOS%20Topology.png)

Now that no device is connected, we enter the "Applications" view to activate and deactivate some app components of ONOS.

- When we select one app component, we can use the buttons on the upper right to "activate", "deactivate", "delete", or "download" the app. We can also use the "upload" button to upload a new app component into ONOS.

![image](../../../assets/ONOS%20Application.png)

ONOS requires the `OpenFlow Base Provider` app component to connect to Open vSwitch over OpenFlow protocol. We should activate it.

We also programmed two app components for the ICS network: [An ICS Forwarding](./org.onosproject.icsdefault.oar) and [An ICS Guard](./org.onosproject.ics.oar). "An ICS Forwarding" forwards the traffic to make the ICS network work normally. However, "An ICS Guard" not only forwards the traffic but also implements adaptive countermeasures against some Denial of Service (DoS) attacks.

- These two app components cannot be both activated. Only choose one of them.
- We activate "An ICS Guard" to protect the ICS network.
- We only activate "An ICS Forwarding" when we want to ignore the DoS attacks. That's useful when we want to do some DoS stress test on the ICS network.

Use the "upload" button to upload these two app components. They are ".oar" archive files. By default, we activate `An ICS Guard`.

![image](../../../assets/ONOS%20App.png)

When we want to update an app component, we first deactivate and delete it, and then upload and activate the new one.
