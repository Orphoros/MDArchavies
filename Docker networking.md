# Give Docker images static IP addresses

## Setup network

Create a network:

```bash
docker network create --gateway 172.19.0.1 --subnet 172.19.0.0/16 microservices
```

## Create containers

In docker-compose.yml:

```yaml
version: '2'
services:
  app:
    image: name:tag
    container_name: myapp
    ports:
      - "8200:8200"
    networks:
      bridge:
	      ipv4_address: 172.19.0.10
    restart: always
networks:
  bridge:
    name: microservices
    external: true
```
## Start and run an image in a network

```bash
docker run -d --name container_name -p 8000:80 --network=microservices --ip=172.19.0.10 user/image:main
```