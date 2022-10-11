# README

## Setup

```bash
# copy service file
sudo cp -r service/* /etc/
sudo systemctl daemon-reload
sudo systemctl enable docker-compose-app.service
```