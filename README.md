# Portainer (Docker Compose)

UI: `https://localhost:9443`

Notes:
- First visit uses a self-signed HTTPS certificate → browser warning is normal.
- Portainer manages the local Docker engine via /var/run/docker.sock.
- Data is persisted in the Docker volume: portainer_data

## Start
```bash
cd ~/docker/portainer
sudo docker compose up -d
```

## Stop
```bash
cd ~/docker/portainer
sudo docker compose down
```

## Restart
```bash
cd ~/docker/portainer
sudo docker compose restart
```

## View logs
```bash
sudo docker logs -n 100 portainer
```

## Update Portainer (recommended way)
```bash
cd ~/docker/portainer
sudo docker compose pull
sudo docker compose up -d
```

## If you need to change ports
Edit `compose.yaml`, then:
`cd ~/docker/portainer
sudo docker compose up -d`
