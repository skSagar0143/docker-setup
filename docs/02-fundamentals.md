# 2. Docker Fundamentals and Architecture

## What is Docker?

Docker is a platform and toolset for building, distributing and running applications in containers. Containers package an application and its required user-space dependencies into a consistent runtime unit. A container still depends on the host kernel and runtime environment.

## Why developers use Docker

- Consistent environments across development, testing and deployment.
- Repeatable builds and releases.
- Process and filesystem isolation.
- Easier distribution of application artifacts.
- Convenient local dependencies such as MySQL, Redis and Kafka.
- Integration with CI/CD pipelines.
- Efficient use of compute compared with running a full guest operating system for every application.

Docker reduces environment differences; it does not eliminate configuration, networking, security, CPU architecture or external-service problems.

## Key terms

| Term | Meaning |
|---|---|
| Docker CLI/client | Command-line interface that sends requests to the Engine |
| Docker Engine/daemon | Manages images, containers, networks, volumes and related objects |
| Image | Immutable, layered template used to create containers |
| Container | An isolated process/runtime instance created from an image |
| Registry | Service that stores and distributes images |
| Repository | Collection of related image versions/tags in a registry |
| Tag | Human-readable image reference such as `1.0` or `alpine` |
| Dockerfile | Instructions used to build an image |
| Build context | Files made available to the image build process |
| Layer | Filesystem changes represented in an image |
| Volume | Docker-managed persistent data storage |
| Bind mount | Maps a host path into a container |
| Network | Enables container connectivity |
| Compose | Tool for defining and operating multi-container applications |

## Image vs container

An image is the packaged template. A container is an instance created from the image. Multiple containers can be created from one image.

A container's writable layer is generally ephemeral. Data that must outlive the container should be stored in a volume or an appropriate external service.

## Docker vs virtual machines

Containers typically share the host kernel and isolate processes; virtual machines run a guest OS on virtualized hardware. Containers usually start faster and use fewer resources, while VMs provide a separate guest kernel and a different isolation boundary. Both approaches can be used together.

Containers are not automatically secure just because they are isolated. Use least privilege, trusted images, patching and sensible runtime configuration.

## Docker architecture overview

1. A developer enters a Docker CLI command.
2. The client sends a request to the Docker Engine.
3. The Engine builds an image, creates a container, configures networking/storage, or performs the requested operation.
4. Images may be pulled from or pushed to a registry.
5. Containers run application processes using the configured image and runtime settings.

## Typical Spring Boot workflow

```text
Source code -> Maven build -> runnable JAR -> Dockerfile -> image
                                                        |
                                                        v
                                                  image registry
                                                        |
                                                        v
                                                    container
```

## Container lifecycle

A typical lifecycle is: create -> start -> running -> stop -> start again -> remove. A container may also exit because its main process finishes or crashes.

Useful commands:

```bash
docker ps
docker ps -a
docker inspect <container>
docker logs <container>
docker stop <container>
docker start <container>
docker rm <container>
```

## Ports explained

In `-p HOST_PORT:CONTAINER_PORT`, the left side is the host port and the right side is the port inside the container.

Example:

```bash
docker run --rm -p 8085:8080 my-spring-app:1.0
```

Access the application via host port 8085. The app must actually listen on container port 8080 and on an appropriate interface.

## What Docker does not do by itself

- It does not automatically make an application highly available.
- It does not automatically manage a multi-host production cluster.
- It does not replace application monitoring, backups or secret management.
- It does not guarantee that an app will connect to a database without correct networking/configuration.
- It does not turn a database volume into a backup.
- It does not replace Kubernetes or another orchestrator where orchestration is required.

## Interview short answers

**What is Docker?** A platform for packaging and running applications in containers.

**Why use it?** To improve consistency, portability, repeatability and deployment workflows.

**Image vs container?** Template vs instance.

**Docker vs VM?** Containers generally share a host kernel; VMs run a guest OS.

**What is a registry?** A service for storing and distributing container images.

**What is a layer?** A reusable filesystem change contributing to an image.

**What is Compose?** A way to define and operate multi-container applications from YAML.
