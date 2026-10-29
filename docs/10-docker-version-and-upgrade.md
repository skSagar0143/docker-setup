# Docker Version Selection, Compatibility and Upgrade Guide

**Last checked:** 29 October 2026  
**Audience:** Windows 10/11 learner using Docker Desktop, PowerShell, Spring Boot and Docker Compose.

## 1. Which version is current?

Docker's official [Docker Desktop release notes](https://docs.docker.com/desktop/release-notes/) listed **Docker Desktop 4.97.0**, released on **26 October 2026**, as the latest release shown when this guide was checked. The same page lists **Docker Engine 29.9.2** in Docker Desktop 4.96.1. Docker Desktop and Docker Engine have separate version numbers, and Desktop component versions are not all identical.

Release availability is rolled out gradually. Check the official release page on the day you install or update; this repository's date-stamped version note is not a live feed.

## 2. Which one should a Windows learner use?

For a Windows 10/11 development machine, use Docker Desktop from the official Docker website if your Windows edition, build, hardware and virtualization configuration meet its current requirements. The Desktop installer manages the bundled Engine and desktop integration. Do not install a separate Linux Engine package inside Windows to update the Engine bundled with Desktop.

- [Install Docker Desktop on Windows](https://docs.docker.com/desktop/setup/install/windows-install/)
- [Docker Desktop release notes](https://docs.docker.com/desktop/release-notes/)
- [Docker Engine installation](https://docs.docker.com/engine/install/)
- [Docker Compose documentation](https://docs.docker.com/compose/)

For a Linux cloud VM, follow Docker's official Engine installation instructions for the exact distribution instead. Do not blindly copy old tutorial commands that install outdated Compose v1 or use distribution packages that lag behind Docker's supported stable channel.

## 3. Check the versions on your own computer

Open PowerShell after Docker Desktop has started:

```powershell
docker --version
docker version
docker compose version
docker buildx version
docker info
docker context ls
```

Interpretation:
- `docker --version`: CLI version only.
- `docker version`: client and server/Engine version details.
- `docker compose version`: Compose plugin version.
- `docker buildx version`: Buildx plugin version.
- `docker info`: Engine status and configuration.
- `docker context ls`: available Docker endpoints and active context.

A modern Docker Desktop install should support the `docker compose` command. If a command is missing, inspect the installed version and official release notes instead of downloading binaries from unofficial sites.

## 4. Update procedure

1. Read the latest official release notes and confirm Windows requirements.
2. Save work and gracefully stop containers that are doing important writes.
3. Back up important database and application data. Volumes are persistent storage, not backups.
4. Use Docker Desktop's update mechanism if the update is offered. If it is not offered yet, download the current installer from Docker's official Windows installation page.
5. Follow installer prompts; restart if requested.
6. Start Docker Desktop and wait until the Engine is ready.
7. Verify versions and run a smoke test:

```powershell
docker version
docker compose version
docker buildx version
docker run --rm hello-world
docker ps
```

8. Start your Compose project and verify logs, health checks, ports and persistent data.

Do not reset Docker Desktop or uninstall it as a first troubleshooting step; these actions can affect local resources. Review the current official guidance and back up important data before any reset or reinstall.

## 5. Recommended practices for this learning repository

- Use `docker compose` (Compose plugin v2+ style), not old standalone `docker-compose` v1 commands.
- Use explicit image tags for learning reproducibility; do not assume `:latest` means safest or best for production.
- For production, maintain a planned patch/update cadence, scan images, test updates, and preserve a rollback artifact.
- Never commit passwords, tokens, private keys or certificates.
- Do not run `docker system prune -a --volumes` without understanding exactly what data could be removed.
- Re-check the official docs whenever installing because supported Windows builds and release versions change.

## 6. Reference repository comparison

The reference [DevOps-Documents Docker setup](https://github.com/ashokitschool/DevOps-Documents/blob/main/02-Docker-Setup.md) is a short Linux VM installation note. It uses distribution package commands and a convenience installation script, and does not document Windows Docker Desktop, version verification, safe upgrades, Compose v2, or a full interview bank. It is useful as a basic Linux setup reference, but it should not be copied as a complete current Docker handbook. This repository keeps the practical learning path and adds current official references and interview material.
