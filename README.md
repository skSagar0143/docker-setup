# Docker Setup — Complete Learning & Interview Guide

A practical, Spring Boot developer–focused Docker handbook covering first-time setup on Windows, daily usage, image/container lifecycle, Dockerfiles, networking, volumes, Compose, registries, security, troubleshooting, and interview preparation.

> **Learning goal:** Be able to explain Docker clearly, containerize a Spring Boot application, run it with supporting services, troubleshoot common failures, and answer theory, practical, and scenario-based interview questions. This repository is a learning guide; verify commands against your installed Docker version and test destructive commands only with disposable data.

## Current-version note

Docker changes frequently. The version guide is date-stamped and links to official release notes; verify the live version before installing or updating. On 29 October 2026, Docker Desktop 4.97.0 was the latest release shown in Docker's official release notes.

## Start here

1. [First-time setup on Windows](docs/01-first-time-setup-windows.md)
2. [Docker fundamentals and architecture](docs/02-fundamentals.md)
3. [Command cheat sheet and daily workflow](docs/03-command-cheatsheet.md)
4. [Dockerfile and Spring Boot](docs/04-dockerfile-spring-boot.md)
5. [Docker Compose, networking and volumes](docs/05-compose-networking-volumes.md)
6. [Registries, image lifecycle and CI/CD](docs/06-images-registry-cicd.md)
7. [Security, troubleshooting and production practices](docs/07-security-troubleshooting.md)
8. [Interview questions, answers and scenarios](docs/08-interview-questions.md)
9. [Practical labs and revision notes](docs/09-practical-labs-and-short-notes.md)
10. [Docker version selection, compatibility and safe upgrades](docs/10-docker-version-and-upgrade.md)

## Learning path

### Beginner
- Understand image vs container, Docker Engine, daemon, CLI, registry, layers, ports and volumes.
- Run a test container and inspect logs.
- Learn how to stop, start, remove and inspect containers.

### Spring Boot developer
- Build a JAR using Maven.
- Write a Dockerfile and build an image.
- Run the API with host/container port mapping.
- Configure runtime values through environment variables.
- Connect Spring Boot to MySQL using Compose service DNS.
- Keep database data in a named volume.

### Interview ready
- Explain Docker vs virtual machines.
- Diagnose startup, networking, database and permission failures.
- Explain multi-stage builds, caching, image size, health checks and secrets.
- Describe how Jenkins builds and publishes an image and how a deployment platform runs it.

## Prerequisites

- Windows 10/11 64-bit with virtualization support.
- Docker Desktop installed and running.
- A terminal: PowerShell, Command Prompt, or Windows Terminal.
- For the Spring Boot lab: Java 21 and Maven, plus a Spring Boot project that produces a runnable JAR.
- Internet access to download images the first time.

## Safety rules

- Never put production passwords, access keys, tokens, private keys or certificates into an image or Git repository.
- Do not expose MySQL, Redis, Docker daemon sockets, or administrative ports publicly without a reviewed security design.
- A Docker volume is persistent storage, **not a backup**.
- `docker compose down -v` can delete named volumes and database data. Use it only when you intentionally want to discard the lab data.
- Images and containers can consume disk space. Remove only resources you recognize.
- For production, pin and update image versions deliberately; do not blindly trust `:latest`.

## Official references

- [Docker documentation](https://docs.docker.com/)
- [Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- [Dockerfile reference](https://docs.docker.com/reference/dockerfile/)
- [Docker Compose documentation](https://docs.docker.com/compose/)
- [Docker CLI reference](https://docs.docker.com/reference/cli/docker/)
- [Docker Hub](https://hub.docker.com/)

## Suggested one-day study order

1. Setup + fundamentals (45 min)
2. Commands + run/inspect/stop a test container (45 min)
3. Dockerfile + Spring Boot image (60 min)
4. Compose + database networking + volumes (60 min)
5. Interview scenarios + labs (60 min)

This is a crash-course plan, not a claim that Docker production expertise can be mastered in one day. Complete the practical labs and explain your own results honestly in interviews.
