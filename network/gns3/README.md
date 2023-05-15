# Install GNS3 Client and Server

- [Install GNS3 Client and Server](#install-gns3-client-and-server)
	- [Prerequisites](#prerequisites)
	- [Install GNS3 Client](#install-gns3-client)
	- [Install GNS3 Server](#install-gns3-server)
		- [Install Docker Engine](#install-docker-engine)
	- [Start GNS3 Server](#start-gns3-server)
	- [Connect GNS3 Client to Server](#connect-gns3-client-to-server)
	- [Security Patches](#security-patches)

GNS3 is a network emulator. It is more customisable than Mininet, and more professional than Packet Tracer.

GNS3 implements client/server architecture.

- A GNS3 server emulates a network in a headless Linux.
- A GNS3 client in a laptop connects to the GNS3 server to visualise, configure, test, and troubleshoot the network.

Docker engine is an optional dependence (plugin) of GNS3 server, but is mandatory to deploy our ICS network.

## Prerequisites

A bare metal Linux distro ([Ubuntu server LTS](https://ubuntu.com/download/server/) recommended) with

- a x86_64/amd64 CPU with at least quad cores (octa cores recommended)
- at least 20GB RAM (32GB recommended)
- at least 30GB HDD/SSD
- (optional) a public IPv4/IPv6 (nice to have)

It is recommended to use your own home desktop (if you have one). In this case, you can install [Ubuntu desktop LTS](https://ubuntu.com/download/desktop/) with a gnome GUI bundled in your home desktop if you also want to play games in it. [Steam](https://repo.steampowered.com/steam/) is officially available in Ubuntu desktop LTS.

(Advanced users only!) If you have an IPC (Industrial Personal Computer) with multiple RJ45 Ethernet interfaces and at least 32GB RAM, you may install ESXi/PVE/unRaid in it to host multiple OSs such as Ubuntu, Windows, and OpenWRT (soft router). Installation instructions can be found on BiliBili and YouTube. However, do this at your own risk!

Thanks to China's IoT network infrastructure construction, Chinese home broadband users should have dynamic public IPv6 assigned to all connected home devices. If you have dynamic public IPv6, you can work at school to remotely connect to your home desktop. If you have a domain name, you can use DDNS (Dynamic DNS) to resolve some addresses in the domain name to your home desktop IPv6. You may [email me](mailto:wangziyao318@163.com) to request a free domain name address if you have public IPv4/IPv6.

## Install GNS3 Client

1. Go to https://gns3.com/ and register an account with an educational email address.
2. Login and then [download the GNS3 client](https://gns3.com/software/download).
3. Install the package.
   - Mac users refer to [this discussion](https://gns3.com/install-error-macos-ventura/) in case of installation error.

## Install GNS3 Server

Ubuntu users refer to the [official guide](https://docs.gns3.com/docs/getting-started/installation/linux/) to install GNS3 server in Ubuntu.

```sh
# add third-party repository and install gns3-server
sudo add-apt-repository ppa:gns3/ppa
sudo apt update
sudo apt install gns3-server

# add current user to groups
sudo usermod -aG ubridge $USER
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
sudo usermod -aG wireshark $USER
```

Generic Linux users (except Ubuntu/Debian) follow the instructions below.

1. Install python3, pip3, [qemu](https://www.qemu.org/), and [libvirt](https://libvirt.org/) using the package manager of your linux distro.
2. Make and install [ubridge](https://github.com/GNS3/ubridge/).
   - Install pcap library (libpcap) and pthread library (libpthread) using your package manager.

```sh
git clone https://github.com/GNS3/ubridge.git
cd ubridge
make
sudo make install

# if "sudo make install" fails (os name not recognised)
sudo mkdir -p /usr/local/bin/
sudo cp ubridge /usr/local/bin/
sudo chmod 755 /usr/local/bin/ubridge
```

3. Make and install [dynamips](https://github.com/GNS3/dynamips/).
   - Install elf library (libelf) and [specified pcap library](https://github.com/GNS3/dynamips/#build-dependencies) (if necessary) using your package manager.

```sh
git clone https://github.com/GNS3/dynamips.git
cd dynamips
mkdir build
cd build
cmake ..
make
sudo make install
```

4. Make and install [vpcs](https://github.com/GNS3/vpcs/).

```sh
git clone https://github.com/GNS3/vpcs.git
cd vpcs/src
./mk.sh 64
sudo cp vpcs /usr/local/bin/
sudo chmod 755 /usr/local/bin/vpcs
```

5. Install [gns3-server](https://github.com/GNS3/gns3-server/) via pip3.

```sh
git clone https://github.com/GNS3/gns3-server.git
cd gns3-server

# you may ignore the pip warning
sudo python3 -m pip install .
```

### Install Docker Engine

Ubuntu users refer to the [official guide](https://docs.docker.com/engine/install/ubuntu/) to install Docker Engine in Ubuntu. The guide also covers installation in CentOS, Debian, Fedora, RHEL, and SLES.

```sh
# uninstall old version of docker engine
sudo apt purge docker docker-engine docker.io containerd runc

# install tools if not exist
sudo apt update
sudo apt install ca-certificates curl gnupg

# import docker's gpg public key
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# add third-party repository
echo "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# install docker
sudo apt update
sudo apt install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

# add current user to docker group
sudo usermod -aG docker $USER
```

## Start GNS3 Server

It is recommended to run gns3server as a service with nonroot user.

- Generic Linux (except Ubuntu/Debian) can only run gns3server as root.

```sh
# change "root" to your username in Ubuntu/Debian
sudo cat > /lib/systemd/system/gns3server.service << EOF
[Unit]
Description=GNS3 server
After=network-online.target
Wants=network-online.target
Conflicts=shutdown.target

[Service]
User=root
Group=root
PermissionsStartOnly=true
AmbientCapabilities=CAP_NET_BIND_SERVICE
EnvironmentFile=/etc/environment
ExecStart=/usr/bin/gns3server
ExecReload=/bin/kill -s HUP $MAINPID
Restart=on-failure
RestartSec=5
LimitNOFILE=16384

[Install]
WantedBy=multi-user.target
EOF

# start gns3-server as a service
sudo systemctl daemon-reload
sudo systemctl enable --now gns3server
```

## Connect GNS3 Client to Server

Refer to [official configuration file](https://docs.gns3.com/docs/using-gns3/administration/gns3-server-configuration-file/) of GNS3 server if you want to tune some parameters.

```
[Server]

; use 0.0.0.0 for IPv4, :: for IPv6
host=::
port=3080

; TCP ports for telnet consoles
console_start_port_range = 5000
console_end_port_range = 5050

; auth username and password
auth=True
user=gns3
password=gns3

; (optional) enable HTTPS with a domain name and SSL certs
ssl=True
certfile=*.pem
certkey=*.key
```

By default, GNS3 server listens on tcp:0.0.0.0:3080, and telnet consoles listen on ports 5000 to 5050. Config firewall to permit the traffic.

```sh
# Ubuntu (ufw)
sudo ufw allow 3080,5000:5050/tcp

# CentOS (firewalld)
sudo firewall-cmd --zone=public --add-port=3080,5000:5050/tcp --permanent
sudo firewall-cmd reload
```

![image](/assets/GNS3%20CS%20Connection.png)

The figure shows how to use GNS3 client to connect to GNS3 server.

By default, GNS3 server uses HTTP auth with username/password: `gns3`/`gns3`. You may configure it over HTTPS if you have a domain name and SSL certs.

## Security Patches

if public IPv4, then must do! if public ipv6, then recommended
how to configure HTTPS or SSH local forwarding