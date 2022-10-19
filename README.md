# README

## Installation

```bash
# install docker
sudo apt-get update
sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg

echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt-get update
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-compose-plugin

sudo service docker start
sudo docker run hello-world

# postinstallation
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker 
docker run hello-world

# autostart
sudo systemctl enable docker.service
sudo systemctl enable containerd.service

# setup wifi AP
[](https://variwiki.com/index.php?title=Wifi_NetworkManager)

sudo apt-get install network-manager
sudo apt-get install dnsmasq
sudo systemctl disable dnsmasq
sudo systemctl stop dnsmasq

#file: /etc/NetworkManager/NetworkManager.conf
[main]
dns=dnsmasq

sudo nmcli con add type wifi ifname wlan0 mode ap con-name WIFI_AP ssid IoT-B
sudo nmcli con modify WIFI_AP 802-11-wireless.band bg
sudo nmcli con modify WIFI_AP 802-11-wireless.channel 1
sudo nmcli con modify WIFI_AP 802-11-wireless-security.key-mgmt wpa-psk
sudo nmcli con modify WIFI_AP 802-11-wireless-security.proto rsn
sudo nmcli con modify WIFI_AP 802-11-wireless-security.group ccmp
sudo nmcli con modify WIFI_AP 802-11-wireless-security.pairwise ccmp
sudo nmcli con modify WIFI_AP 802-11-wireless-security.psk superSecure
sudo nmcli con modify WIFI_AP ipv4.method shared
sudo nmcli con up WIFI_AP

# by default 10.42.0.x/24 subnet is used by network manager dhcp server
# nmcli con modify <name> ipv4.addr <ipaddress/prefix>
# nmcli con modify WIFI_AP ipv4.addr 192.168.5.1/24

# show existing connections
sudo nmcli con show
sudo nmcli con down <name>
sudo nmcli con up <name>
sudo nmcli con delete <name>

#file: /etc/NetworkManager/system-connections/WIFI_AP
```

## Setup

```bash
git clone git@github.com:rpeterer/IoT-BG.git
cd IoT-BG

# copy service file
sudo cp -r service/* /etc/
sudo systemctl daemon-reload
sudo systemctl enable docker-compose-app.service
```

## Test

```bash
mosquitto_sub -t "msg/0"
mosquitto_pub -t "msg/0" -m "Hallo Welt"
```

## Network

```shell
sudo nano /etc/cloud/cloud.cfg.d/99-disable-network-config.cfg
network: {config: disabled}

sudo nano /etc/netplan/01-netcfg.yaml

network:
  version: 2
  renderer: networkd
  ethernets:
    eth0:
      dhcp4: yes
      optional: true
      addresses:
        - 10.0.0.15/24:
            lifetime: 0
            label: "maas"
```
