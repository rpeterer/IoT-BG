# README

## Setup

```bash
# copy service file
sudo cp -r service/* /etc/
sudo systemctl daemon-reload
sudo systemctl enable docker-compose-app.service
```

## Test

```bash
mosquitto_sub -t "msg/0"
mosquitto_pub -t "msg/1" -m "Hallo Welt"
```