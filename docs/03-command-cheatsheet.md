# 3. Docker Command Cheat Sheet

Commands are examples. Replace placeholders such as `<container>` with real names or IDs. Run commands from PowerShell, Command Prompt or a compatible terminal.

## Verify and inspect

```bash
docker --version
docker version
docker info
docker context ls
docker ps
docker ps -a
docker images
docker image ls
docker volume ls
docker network ls
docker system df
```

## Run a container

```bash
docker run --rm hello-world
docker run -d --name web-demo -p 8085:80 nginx
docker run -it --rm alpine sh
```

- `-d`: detached/background mode.
- `-it`: interactive terminal.
- `--rm`: remove the container after it exits.
- `--name`: assign a predictable container name.
- `-p host:container`: publish a container port to the host.
- `-e KEY=value`: set an environment variable.
- `--mount` or `-v`: mount storage.

## Container lifecycle

```bash
docker ps
docker ps -a
docker stop <container>
docker start <container>
docker restart <container>
docker pause <container>
docker unpause <container>
docker rm <container>
docker rm -f <container>
docker inspect <container>
docker stats
docker top <container>
```

Use `docker rm -f` carefully: it forcibly stops and removes the named container.

## Logs and execution

```bash
docker logs <container>
docker logs --tail 100 <container>
docker logs -f <container>
docker exec -it <container> sh
docker exec -it <container> bash
```

A minimal image may not include Bash. Try `sh` if `bash` is unavailable. `docker exec` requires a running container.

## Images

```bash
docker pull nginx:stable
docker image inspect nginx:stable
docker history nginx:stable
docker build -t booking-app:1.0 .
docker tag booking-app:1.0 <registry-user>/booking-app:1.0
docker image rm <image>
docker image prune
```

Use explicit version tags where appropriate. `docker image prune` removes dangling images by default; review any broader prune command before use.

## Build a Spring Boot JAR and image

```bash
mvn clean package
docker build -t booking-app:1.0 .
docker run -d --name booking-app -p 8080:8080 booking-app:1.0
docker logs -f booking-app
```

Stop/remove the lab container when finished:

```bash
docker stop booking-app
docker rm booking-app
```

## Networks

```bash
docker network ls
docker network create app-net
docker network inspect app-net
docker network connect app-net <container>
docker network disconnect app-net <container>
docker network rm app-net
```

## Volumes

```bash
docker volume ls
docker volume create app-data
docker volume inspect app-data
docker volume rm app-data
```

Do not remove a volume until you are certain its data is disposable or backed up.

## Docker Compose

Run these commands in the directory containing `compose.yaml`:

```bash
docker compose config
docker compose up -d
docker compose up -d --build
docker compose ps
docker compose logs -f
docker compose logs -f app
docker compose exec app sh
docker compose stop
docker compose start
docker compose restart app
docker compose down
```

`docker compose down -v` also removes project-managed named volumes and can delete database data. Use only when intentional.

## Registry workflow

```bash
docker login
docker tag booking-app:1.0 <dockerhub-username>/booking-app:1.0
docker push <dockerhub-username>/booking-app:1.0
docker pull <dockerhub-username>/booking-app:1.0
docker logout
```

Do not put registry passwords into scripts or Git. Use secure credential storage or CI/CD credentials.

## Cleanup and disk usage

```bash
docker system df
docker container prune
docker image prune
docker network prune
docker volume ls
```

Prune commands delete unused resources. Review what is unused and whether any volume contains data you need before cleanup.

## Quick troubleshooting commands

```bash
docker ps -a
docker logs <container>
docker inspect <container>
docker port <container>
docker stats
docker events
docker compose config
docker compose logs --tail 200
```
