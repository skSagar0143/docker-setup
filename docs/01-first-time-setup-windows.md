# 1. First-Time Docker Setup on Windows

## What you are setting up

Docker Desktop provides a convenient way to run Docker on Windows. On typical Windows 10/11 developer machines, Docker Desktop uses the WSL 2 backend or a supported Hyper-V configuration. The Docker CLI sends commands to the Docker Engine. You usually do not need to install a separate Linux server yourself.

## Before installing

1. Check that Windows is supported by the current Docker Desktop release.
2. Ensure CPU virtualization is enabled in BIOS/UEFI.
3. Check Docker's current system requirements in the official documentation.
4. If using WSL 2, ensure the WSL components and kernel are installed and current.
5. Ensure you have enough disk space and RAM. Containers, images and database volumes use local storage.

Official install instructions: https://docs.docker.com/desktop/setup/install/windows-install/

## First-time installation

1. Download Docker Desktop only from the official Docker website.
2. Run the installer and choose the recommended backend for your system (commonly WSL 2).
3. Restart Windows if requested.
4. Start Docker Desktop and wait until it reports that the engine is running.
5. Accept the required terms and complete the initial setup.
6. Open PowerShell or Windows Terminal.

Do not download Docker installers from unofficial mirror sites.

## Verify installation

Run these commands one at a time:

```powershell
docker --version
docker version
docker info
docker context ls
```

- `docker --version` checks the CLI version.
- `docker version` reports client and server versions; a daemon connection is needed for server details.
- `docker info` displays engine configuration and resource details.
- `docker context ls` lists configured Docker endpoints.

Run a test container:

```powershell
docker run --rm hello-world
```

Docker may download the image first. A successful run prints a message explaining that Docker is working. `--rm` removes the stopped test container afterward.

## Start Docker on later days

1. Start Docker Desktop from the Windows Start menu.
2. Wait until the engine is running.
3. Open a terminal and run:

```powershell
docker info
docker ps
```

You do not normally need to reinstall Docker or recreate images each day.

## Stop Docker workloads safely

List containers:

```powershell
docker ps
```

Stop one container:

```powershell
docker stop <container-name-or-id>
```

For a Compose project, go to the folder containing `compose.yaml` and run:

```powershell
docker compose stop
```

or stop and remove the project containers/network while retaining named volumes:

```powershell
docker compose down
```

Stopping Docker Desktop itself is separate from stopping individual application containers. Avoid abruptly terminating workloads that are writing important data.

## Daily start-up checklist

- [ ] Docker Desktop is running.
- [ ] `docker info` succeeds.
- [ ] Correct Docker context is selected.
- [ ] Required images exist or can be pulled.
- [ ] Required containers are running: `docker ps`.
- [ ] Ports required by the application are free.
- [ ] Persistent volumes have not been deleted.

## Common first-time problems

### 'Cannot connect to the Docker daemon'
Start Docker Desktop, wait for engine startup, and verify the current context with `docker context ls`.

### WSL 2 is not installed or needs updating
Follow the current Microsoft and Docker Desktop documentation for your Windows version. Do not copy random commands from old tutorials without checking compatibility.

### Port is already allocated
Another process or container may be using the host port. Check `docker ps`, stop the conflicting lab container if appropriate, or choose a different host port.

### Image pull fails
Check internet access, registry availability, proxy settings, image name/tag, and registry authentication if the image is private.

### Docker is using too much RAM or disk
Stop unneeded containers; inspect images and volumes before deleting anything. Do not remove volumes containing data you need.

## Uninstalling

Uninstalling Docker Desktop and deleting Docker data are different operations. Read current official instructions first. Removing Docker's data can delete local images, containers and volumes. Back up anything important.
