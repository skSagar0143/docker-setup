# 5. Docker Compose, Networking and Volumes

## What is Docker Compose?

Compose defines a multi-container application in a YAML file, commonly named `compose.yaml`. It can configure services, networks, volumes, environment variables, ports, health checks and build instructions.

## Spring Boot + MySQL example

Assumptions:
- The Spring Boot Dockerfile is in the same folder.
- The application listens on port 8080.
- Spring Boot uses standard datasource environment variables.
- The example database is for local learning only.

Create `.env` beside `compose.yaml`:

```dotenv
DB_PASSWORD=replace_with_a_strong_local_password
MYSQL_ROOT_PASSWORD=replace_with_another_strong_password
```

Add `.env` to `.gitignore`. Do not commit it.

Create `compose.yaml`:

```yaml
services:
  mysql:
    image: mysql:8.4
    environment:
      MYSQL_DATABASE: booking_db
      MYSQL_USER: booking_user
      MYSQL_PASSWORD: ${DB_PASSWORD}
      MYSQL_ROOT_PASSWORD: ${MYSQL_ROOT_PASSWORD}
    ports:
      - "3307:3306"
    volumes:
      - mysql-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "mysqladmin", "ping", "-h", "localhost"]
      interval: 10s
      timeout: 5s
      retries: 10

  app:
    build: .
    ports:
      - "8080:8080"
    environment:
      SPRING_DATASOURCE_URL: jdbc:mysql://mysql:3306/booking_db
      SPRING_DATASOURCE_USERNAME: booking_user
      SPRING_DATASOURCE_PASSWORD: ${DB_PASSWORD}
    depends_on:
      mysql:
        condition: service_healthy

volumes:
  mysql-data:
```

Validate and start:

```bash
docker compose config
docker compose up -d --build
docker compose ps
docker compose logs -f app
```

Stop:

```bash
docker compose down
```

The named volume remains after ordinary `docker compose down`. To remove the volume too, `docker compose down -v` may be used only when intentionally discarding its data.

This sample is not a production secrets pattern. Production credentials should come from a suitable secrets manager, and database ports should not be exposed unnecessarily.

## Why use service names instead of localhost?

Within the Compose network, the application can reach MySQL using the service name `mysql` and its container port 3306. Inside a container, `localhost` refers to that same container. Host port 3307 is for connections originating from the host machine.

## depends_on and readiness

Short-form `depends_on` primarily establishes startup ordering. It does not necessarily mean a dependency is ready to accept connections. A health check combined with `condition: service_healthy` can improve startup coordination, but the application should still handle transient database failures and reconnect appropriately.

## Networks

Compose creates a project network by default. Services on the same network can usually discover one another by service name.

Useful commands:

```bash
docker network ls
docker network inspect <network>
docker compose exec app sh
docker compose exec mysql sh
```

Do not assume every image includes Bash, curl, ping or other diagnostic utilities.

## Volumes vs bind mounts

### Named volume
Docker manages the storage location. Suitable for persistent database data.

### Bind mount
Maps an explicit host path into a container. Useful for local development, configuration files or source code, but host paths and permissions can differ between systems.

Example bind mount syntax:

```yaml
volumes:
  - ./config:/app/config:ro
```

The `:ro` suffix makes the mount read-only from the container's perspective.

## Persistence and backups

A volume allows data to survive container replacement, but it can still be deleted or corrupted. Important data needs tested backups and a recovery plan.

## Compose command reference

```bash
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
docker compose config
```

## Common Compose failures

- **Variable is blank:** check .env location, variable spelling and shell environment.
- **Connection refused:** database may not be ready, or hostname/port is wrong.
- **Name cannot resolve:** services may not share a network or the service name may be wrong.
- **Port already allocated:** choose another host port or stop the conflicting process.
- **Database resets:** check whether a named volume is mounted and whether it was removed.
- **Application starts before DB readiness:** add appropriate health checks and resilient connection handling.
