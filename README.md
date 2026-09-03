# Portainer (Docker Compose)

UI: `https://localhost:9443`

Notes:
- First visit uses a self-signed HTTPS certificate → browser warning is normal.
- Portainer manages the local Docker engine via /var/run/docker.sock.
- Data is persisted in the Docker volume: portainer_data
- The ports publish on all interfaces and Portainer holds the Docker socket.
  Read "Security note" at the bottom before running this on any network you do
  not control.

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
```bash
cd ~/docker/portainer
sudo docker compose up -d
```

## Security note: the ports publish on every interface

`compose.yaml` maps `"9443:9443"` and `"8000:8000"` with no host address, so
Docker binds them on `0.0.0.0`. The UI is then reachable from any machine that
can route to this host, not just from the host itself.

That matters more here than for a typical container, because Portainer mounts
`/var/run/docker.sock`. Anyone who gets past the login can start a privileged
container, so access to the UI is effectively root on the host. The Portainer
password is the only thing in the way.

This is fine on a machine you control on a network you trust, which is how it is
used here. **Before running this anywhere else**, especially on a shared,
office, public or cloud network, bind it to loopback:

```yaml
    ports:
      - "127.0.0.1:9443:9443"   # https UI, this machine only
```

Drop the `8000` mapping at the same time unless you actually use Portainer Edge
agents; it is the agent tunnel and is otherwise unused.

With that change the UI is still available at `https://localhost:9443` on the
host. To reach it from another machine, tunnel rather than republish the port:

```bash
ssh -N -L 9443:localhost:9443 <user>@<host>
```

Note that `ufw` will NOT protect a published Docker port. Docker writes its own
iptables rules that are evaluated first, so a `ufw deny 9443` appears to succeed
and changes nothing. Binding to `127.0.0.1` is the fix, not a firewall rule.
