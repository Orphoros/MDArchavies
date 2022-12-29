# Setup Portainer for Docker

## Docker compose file

```bash
touch docker-compose.yml
```

```yaml
version: '2'
services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    ports:
      - "8000:8000"
      - "9000:9000"
    networks:
      bridge:
       ipv4_address: 172.19.0.11
    restart: always
    volumes:
     - /var/run/docker.sock:/var/run/docker.sock
     - portainer_data:/data
networks:
  bridge:
    name: microservices
    external: true
volumes:
  portainer_data: {}
```