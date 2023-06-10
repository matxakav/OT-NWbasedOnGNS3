# Install and Use GNS3 Client and Server

- [Install and Use GNS3 Client and Server](#install-and-use-gns3-client-and-server)
	- [Prerequisites](#prerequisites)
	- [Install GNS3 Client](#install-gns3-client)
	- [Install GNS3 Server](#install-gns3-server)
		- [Install Docker Engine](#install-docker-engine)
	- [Run GNS3 Server](#run-gns3-server)
	- [Connect GNS3 Client to Server](#connect-gns3-client-to-server)
	- [Configure GNS3 Client Workspace](#configure-gns3-client-workspace)
	- [Security Patches](#security-patches)
		- [SSH over Public-Key Authentication](#ssh-over-public-key-authentication)
		- [GNS3 Server over SSH Local Forwarding](#gns3-server-over-ssh-local-forwarding)

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

It is recommended to use your own home desktop (if you have one). In this case, you can install [Ubuntu desktop LTS](https://ubuntu.com/download/desktop/) with a gnome GUI bundled if you also want to play games in it. [Steam](https://repo.steampowered.com/steam/) is officially available in Ubuntu desktop LTS and most steam games can run in Linux using Proton – a fork of [wine](https://www.winehq.org/).

(Advanced users only!) If you have an IPC (Industrial Personal Computer) with multiple RJ45 Ethernet interfaces and at least 32GB RAM, you may install ESXi/PVE/unRaid in it to host multiple OSs such as Ubuntu, Windows, and OpenWRT (soft router). Installation instructions can be found on BiliBili and YouTube. However, do this at your own risk!

Thanks to China's IoT network infrastructure construction, Chinese home broadband users should have dynamic public IPv6 assigned to all connected home devices. If you have dynamic public IPv6, you can work at school to remotely connect to your home desktop. If you have a domain name, you can use DDNS (Dynamic DNS) to resolve some addresses in the domain name to your home desktop IPv6. You may [email me](mailto:wangziyao318@163.com) to request a free domain name address only if you have public IPv4/IPv6.

## Install GNS3 Client

1. [Download the GNS3 client](https://github.com/GNS3/gns3-gui/releases/) from GitHub release. Do not download from GNS3 official website because sometimes it will prompt "Download Request Received" without showing you the download link.
2. Install the package.
   - Mac users on Ventura and later refer to [this discussion](https://gns3.com/install-error-macos-ventura/) in case of installation error.

## Install GNS3 Server

Ubuntu/Debian users refer to the [official guide](https://docs.gns3.com/docs/getting-started/installation/linux/) to install GNS3 server in Ubuntu/Debian.

```sh
# add third-party repository and install gns3-server
sudo add-apt-repository ppa:gns3/ppa
sudo apt update
sudo apt install gns3-server

# add current user to groups
sudo usermod -aG ubridge $USER
sudo usermod -aG libvirt $USER
sudo usermod -aG kvm $USER
```

Generic Linux users (except Ubuntu/Debian) follow the instructions below.

1. Install python3, pip3, [qemu](https://www.qemu.org/), and [libvirt](https://libvirt.org/) using the package manager of your Linux distro.
2. Make and install [ubridge](https://github.com/GNS3/ubridge/).
   - Install pcap library (libpcap) and pthread library (libpthread) using your package manager.

```sh
git clone https://github.com/GNS3/ubridge.git
cd ubridge
make
sudo make install

# if "sudo make install" fails (OS name not recognised)
sudo mkdir -p /usr/local/bin/
sudo cp ubridge /usr/local/bin/
sudo chown root:root /usr/local/bin/ubridge
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

# you may ignore the pip warning and refer to the warning content to add extra arguments to bypass it
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

## Run GNS3 Server

It is recommended to run gns3server as a service with **nonroot** user.

- Generic Linux (except Ubuntu/Debian) following this instruction can only run gns3server as root.

```sh
# Remove "User=" and "Group=" lines in generic Linux (except Ubuntu/Debian)
# Note that gns3server.service should be overwritten, reloaded and restarted after each APT upgrade of gns-server in Ubuntu/Debian
sudo cat > /lib/systemd/system/gns3server.service << EOF
[Unit]
Description=GNS3 server
After=network-online.target
Wants=network-online.target
Conflicts=shutdown.target

[Service]
User=$(echo -n $USER)
Group=$(echo -n $USER)
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

Upon start, GNS3 server creates `~/GNS3/` to store project files and `~/.config/GNS3/` to store configurations.

- If you run GNS3 server as root, then `~` becomes `/root/`. In this case, you should `sudo su` to root user to have permission to `cd` into this directory.
- The configuration file of GNS3 server version 2.2.X is `~/.config/GNS3/2.2/gns3_server.conf`. You should manually create it first to customise the GNS3 server.

## Connect GNS3 Client to Server

Refer to [official configuration file](https://docs.gns3.com/docs/using-gns3/administration/gns3-server-configuration-file/) of GNS3 server if you want to tune some parameters.

```conf
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

; (optional) enable HTTPS with SSL certs of a domain name
;ssl=True
;certfile=*.pem
;certkey=*.key
```

By default, GNS3 server listens on tcp:0.0.0.0:3080, and telnet consoles listen on ports 5000 to 5050.

Config firewall to temporarily permit the traffic only if your firewall is enabled by default. This is often the case when you are using a VPS (Virtual Private Server).

```sh
# Ubuntu (ufw)
# check if firewall is enabled
sudo ufw status
# allow openssh-server and gns3-server
sudo ufw allow 22/tcp
sudo ufw allow 3080,5000:5050/tcp

# CentOS (firewalld)
# firewalld allows for temporary rules that will disappear after reboot
# this openssh-server rule is persistent
sudo firewall-cmd --zone=public --add-service=ssh --permanent
sudo firewall-cmd --reload
# this gns3-server rule will disappear after reboot
sudo firewall-cmd --zone=public --add-port=3080,5000:5050/tcp
```

<br/>

![image](../assets/GNS3%20CS%20Connection.png)

<p align="center"><b>Figure 1</b> Connect GNS3 Client to GNS3 Server</p>

<br/>

Figure 1 shows how to use GNS3 client to connect to GNS3 server. Say the server IP is `192.168.0.1`. By default, GNS3 server uses HTTP auth with username/password: `gns3`/`gns3`.

## Configure GNS3 Client Workspace

<br/>

![image](../assets/GNS3%20Client%20Workspace.png)

<p align="center"><b>Figure 2</b> Configure GNS3 Client Workspace</p>

<br/>

As shown in Figure 2, When you create a new project, it's advised to enable `Show the grid`, `Snap to grid`, and `Show interface labels` from the menu bar of GNS3 client. Besides, you can use `Fit in view` to automatically zoom the workspace to display all nodes.

## Security Patches

If your GNS3 server listens on a public IPv4/IPv6, then security patches **MUST** be done to prevent your Linux server from attacks. If you ignore this and continue to use GNS3 server over HTTP in public network, then an attacker may use MITM (Man-in-the-Middle) / brute-force attack to retrieve your gns3-server username/password, and then gets into your host console by utilising some gns3-server and docker vulnerabilities.

We configure SSH over public-key authentication and forward gns3-server traffic over SSH.

### SSH over Public-Key Authentication

It is strongly advised to remove password authentication for openssh-server in your home desktop.

1. Generate a RSA key pair in your laptop. By default, the public key is `~/.ssh/id_rsa.pub` and the private key is `~/.ssh/id_rsa`.

```sh
# you can safely press "Return" for all prompts
ssh-keygen

# the private key can only be read by yourself
chmod 400 ~/.ssh/id_rsa
```

2. SSH into your home desktop, and append your public key into a list of authorized keys.

```sh
cat >> ~/.ssh/authorized_keys < EOF
# your public key here
EOF
```

3. Modify openssh-server configuration in your home desktop. You may refer to the [manual page](https://www.man7.org/linux/man-pages/man5/sshd_config.5.html) for `sshd_config`.
```sh
# you may add a line "PermitRootLogin prohibit-password" if your server only has the root user
sudo cat > /etc/ssh/sshd_config < EOF
AuthenticationMethods publickey
PasswordAuthentication no
Banner /etc/ssh/sshd_banner
Subsystem sftp /usr/lib/openssh/sftp-server
EOF
```

4. Customise your SSH banner and then restart openssh-server. Here I give mine.

```sh
sudo cat > /etc/ssh/sshd_banner < EOF
 _              _   _                                         _       _
| |__     ___  | | | |   ___       __      __   ___    _ __  | |   __| |
| '_ \   / _ \ | | | |  / _ \      \ \ /\ / /  / _ \  | '__| | |  / _` |
| | | | |  __/ | | | | | (_) |  _   \ V  V /  | (_) | | |    | | | (_| |
|_| |_|  \___| |_| |_|  \___/  ( )   \_/\_/    \___/  |_|    |_|  \__,_|
                               |/
EOF

# restart openssh-server
sudo systemctl restart sshd
```

5. Enable firewall on your home desktop and permit SSH traffic.

```sh
# Ubuntu (ufw)
sudo ufw allow 22/tcp
sudo ufw enable

# CentOS (firewalld)
sudo firewall-cmd --zone=public --add-server=ssh --permanent
sudo firewall-cmd reload
sudo systemctl enable --now firewalld
```

6. Now, in your laptop, you would be able to SSH into your home desktop without typing a password.

```sh
# say your server IP is "192.168.0.1" and your username in the server is "yourname"
ssh yourname@192.168.0.1
```

7. If you have reinstalled your server with the same IP, then you may want to remove the `known_hosts*` files in your laptop to bypass "someone is doing something nasty".

```sh
rm ~/.ssh/known_hosts ~/.ssh/known_hosts.old
```

### GNS3 Server over SSH Local Forwarding

Local forwarding forwards a TCP port in your laptop over SSH to a TCP port in your home deaktop.

Create a `~/.ssh/config` file in your laptop to tailor your SSH experience. Refer to [manual page](https://linux.die.net/man/5/ssh_config/) for `ssh_config`.

- You should define at least 3 hosts: "yourserver" to SSH into your home desktop, and "up" and "down" to manage local forwarding.
- You can add more `LocalForward` entries for other insecure services such as RDP (Remote Desktop Protocol) over 3389/tcp and Samba over 445/tcp.

```
Host yourserver
	HostName 192.168.0.1
	User yourname
Host down
	HostName 192.168.0.1
	user yourname
	ControlPath ~/.ssh/control.socket
Host up
	HostName 192.168.0.1
	User yourname
	ControlMaster yes
	ControlPath ~/.ssh/control.socket
	ExitOnForwardFailure yes
	ReKeyLimit 16G
	LocalForward 127.0.0.1:3080 127.0.0.1:3080
	LocalForward 127.0.0.1:5001 127.0.0.1:5001
	LocalForward 127.0.0.1:5002 127.0.0.1:5002
	LocalForward 127.0.0.1:5003 127.0.0.1:5003
	LocalForward 127.0.0.1:5004 127.0.0.1:5004
	LocalForward 127.0.0.1:5005 127.0.0.1:5005
	LocalForward 127.0.0.1:5006 127.0.0.1:5006
	LocalForward 127.0.0.1:5007 127.0.0.1:5007
	LocalForward 127.0.0.1:5008 127.0.0.1:5008
	LocalForward 127.0.0.1:5009 127.0.0.1:5009
	LocalForward 127.0.0.1:5010 127.0.0.1:5010
	LocalForward 127.0.0.1:5011 127.0.0.1:5011
	LocalForward 127.0.0.1:5012 127.0.0.1:5012
	LocalForward 127.0.0.1:5013 127.0.0.1:5013
	LocalForward 127.0.0.1:5014 127.0.0.1:5014
	LocalForward 127.0.0.1:5015 127.0.0.1:5015
	LocalForward 127.0.0.1:5016 127.0.0.1:5016
	LocalForward 127.0.0.1:5017 127.0.0.1:5017
	LocalForward 127.0.0.1:5018 127.0.0.1:5018
	LocalForward 127.0.0.1:5019 127.0.0.1:5019
	LocalForward 127.0.0.1:5020 127.0.0.1:5020
	LocalForward 127.0.0.1:5021 127.0.0.1:5021
	LocalForward 127.0.0.1:5022 127.0.0.1:5022
	LocalForward 127.0.0.1:5023 127.0.0.1:5023
	LocalForward 127.0.0.1:5024 127.0.0.1:5024
	LocalForward 127.0.0.1:5025 127.0.0.1:5025
	LocalForward 127.0.0.1:5026 127.0.0.1:5026
	LocalForward 127.0.0.1:5027 127.0.0.1:5027
	LocalForward 127.0.0.1:5028 127.0.0.1:5028
	LocalForward 127.0.0.1:5029 127.0.0.1:5029
	LocalForward 127.0.0.1:5030 127.0.0.1:5030
	LocalForward 127.0.0.1:5031 127.0.0.1:5031
	LocalForward 127.0.0.1:5032 127.0.0.1:5032
	LocalForward 127.0.0.1:5033 127.0.0.1:5033
	LocalForward 127.0.0.1:5034 127.0.0.1:5034
	LocalForward 127.0.0.1:5035 127.0.0.1:5035
	LocalForward 127.0.0.1:5036 127.0.0.1:5036
	LocalForward 127.0.0.1:5037 127.0.0.1:5037
	LocalForward 127.0.0.1:5038 127.0.0.1:5038
	LocalForward 127.0.0.1:5039 127.0.0.1:5039
	LocalForward 127.0.0.1:5040 127.0.0.1:5040
	LocalForward 127.0.0.1:5041 127.0.0.1:5041
	LocalForward 127.0.0.1:5042 127.0.0.1:5042
	LocalForward 127.0.0.1:5043 127.0.0.1:5043
	LocalForward 127.0.0.1:5044 127.0.0.1:5044
	LocalForward 127.0.0.1:5045 127.0.0.1:5045
	LocalForward 127.0.0.1:5046 127.0.0.1:5046
	LocalForward 127.0.0.1:5047 127.0.0.1:5047
	LocalForward 127.0.0.1:5048 127.0.0.1:5048
	LocalForward 127.0.0.1:5049 127.0.0.1:5049
	LocalForward 127.0.0.1:5050 127.0.0.1:5050
	LocalForward 127.0.0.1:8101 127.0.0.1:8101
	LocalForward 127.0.0.1:8181 127.0.0.1:8181
Host onos
	HostName 127.0.0.1
	User onos
	HostKeyAlgorithms +ssh-rsa
	Port 8101
	StrictHostKeyChecking no
	UserKnownHostsFile /dev/null
Host *
	Protocol 2
	ServerAliveInterval 30
	ServerAliveCountMax 2
```

The configuration above in `~/.ssh/config` makes it easier to use ssh, scp, and rsync.

```sh
# ssh into your home desktop, "yourserver" is just an alias and you can change it if you like
ssh yourserver

# scp
scp ./file_in_your_laptop yourserver:/home/yourname/
scp yourserver:/home/yourname/file_in_your_home_desktop .

# rsync is similar to scp
```

Define 3 alias in your laptop console to manage SSH local forwarding.

- Different shells have different environment files. In zsh, it is `~/.zshrc`.
- Mac users are recommended to use [oh-my-zsh](https://ohmyz.sh/) with [zsh-autosuggestions](https://github.com/zsh-users/zsh-autosuggestions/) and [zsh-syntax-highlighting](https://github.com/zsh-users/zsh-syntax-highlighting/) plugins.

```sh
alias foo='ssh yourserver'
alias up='ssh -CfNg up'
alias down='ssh down -O exit'
```

Now, in the laptop console, you can type `up` to set up the local forwarding, and type `down` to close it. You can also type `foo` to SSH into your home desktop.

For GNS3 server to make use of local forwarding, we use this `~/.config/GNS3/2.2/gns3_server.conf` configuration in the home desktop.

- Note that we start the console port from `5001`. This is because port `5000` is occupied in our Mac laptop, so we discarded that port. It is up to you to decide your console port range, and GNS3 server should have at least 40 available ports for our ICS network to start.
- Note that we also add port 8101 and 8181 and a host "onos", which is for the ONOS SDN controller.

```conf
[Server]
host=127.0.0.1
port=3080
console_start_port_range = 5001
console_end_port_range = 5050
auth = True
user = gns3
password = gns3
```

When SSH local forwarding is `up`, you can use GNS3 client to connect to the GNS3 server at `tcp:127.0.0.1:3080` in your laptop.