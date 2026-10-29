# 7. Docker Security, Troubleshooting and Best Practices

## Security checklist

- Use trusted, maintained base images.
- Rebuild images to incorporate security updates.
- Run as a non-root user where practical.
- Keep the runtime image minimal.
- Do not bake secrets into Dockerfile, image layers, labels, build arguments or source control.
- Use least privilege and avoid privileged containers unless truly required.
- Avoid mounting the Docker socket into application containers.
- Publish only required ports.
- Use read-only mounts where suitable.
- Scan images and dependencies with approved tooling.
- Keep Docker Desktop and the host OS updated.
- Treat untrusted images and scripts cautiously.
- Protect registry credentials and private repositories.

## Secrets

Dockerfile `ENV` and `ARG` are not a safe way to store secrets. A value may appear in image metadata, build history, logs or CI records. Pass runtime secrets through an appropriate secrets mechanism. For local labs, a gitignored .env file can be convenient, but it is not a production secrets manager.

If a credential is accidentally committed or published, rotate/revoke it immediately. Removing it from the latest file does not erase all copies from Git history or caches.

## Non-root user

Running the app as a non-root user can reduce the impact of some compromises. Ensure required files and directories are readable/writable by that user. Exact user setup depends on the base image and application needs.

## Troubleshooting flow

Use this order:
1. Confirm the Docker Engine is available.
2. Inspect container status and exit code.
3. Read application logs.
4. Inspect environment variables and configuration without exposing secrets.
5. Check ports and listening interfaces.
6. Check network membership and DNS names.
7. Check mounted files and volume permissions.
8. Check resource limits and host resource availability.
9. Reproduce with a minimal test where possible.
10. Apply one change at a time and verify.

## Common errors and fixes

### Cannot connect to Docker daemon
Start Docker Desktop, wait for startup and check the selected Docker context.

### Container exits immediately
Use `docker ps -a`, `docker logs <name>`, and `docker inspect <name>`. Containers exit when their main process finishes or fails.

### Port is already allocated
Use another host port, or identify and stop the process/container using that port.

### Spring Boot starts but cannot connect to MySQL
Check JDBC URL, hostname, port, credentials, network, database readiness and logs. In Compose, use the service name and container port.

### Works on host but not in container
Check environment variables, filesystem paths, runtime Java version, port binding, OS differences, architecture and dependency reachability.

### Permission denied on mounted files
Check host file permissions and the UID/GID used inside the container. Avoid solving it by running everything as root without understanding the cause.

### Image pull denied
Check image spelling, tag, registry permissions, login state and whether the repository is private.

### Image build fails at COPY
Check build context, file path, .dockerignore and case-sensitive path differences.

### Container is slow or stops under load
Check `docker stats`, host RAM/CPU, JVM memory settings, logs and application bottlenecks. Resource limits and JVM behavior should be configured intentionally.

### Disk usage is high
Use `docker system df` and inspect images, containers and volumes. Do not prune volumes blindly.

## Health checks vs application readiness

A container health check indicates a configured check's result; it does not by itself guarantee that every downstream dependency is healthy. Spring Boot Actuator liveness/readiness endpoints and orchestrator probes have distinct purposes. Configure checks that reflect the actual service requirements.

## Production considerations

- Externalize environment-specific configuration.
- Use secrets management.
- Use persistent storage and backups for stateful workloads.
- Configure logs and metrics.
- Apply resource limits where appropriate.
- Plan image patching and vulnerability response.
- Use TLS and restrict network exposure.
- Establish deployment verification and rollback procedures.
- Test restore procedures, not just backup creation.
- Understand the difference between container restart policies and application-level resilience.

## Cleanup carefully

```bash
docker system df
docker ps -a
docker images
docker volume ls
```

Inspect resources before using prune commands. Removing a container, image, network or volume has different effects. A volume may contain database data that cannot be reconstructed.
