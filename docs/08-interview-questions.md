# 8. Docker Interview Questions, Answers and Scenarios

This bank is designed for a Java/Spring Boot developer interview. It covers fundamentals, commands, Dockerfile, Compose, networking, persistence, CI/CD, security and troubleshooting. No list can guarantee every question an interviewer might ask; use the concepts to reason through unfamiliar scenarios.

## Fundamentals

### 1. What is Docker?
Docker is a platform and toolset used to build, distribute and run applications in containers.

### 2. Why use Docker with Spring Boot?
It packages the application and runtime dependencies into a repeatable artifact, reduces environment drift and supports consistent deployment and CI/CD.

### 3. What is an image?
A layered, reusable template from which containers are created.

### 4. What is a container?
An isolated runtime instance created from an image. It can be running or stopped.

### 5. Image vs container?
Image is the template; container is the instance. One image can create multiple containers.

### 6. Docker vs virtual machine?
Containers typically share the host kernel and isolate processes. VMs run a guest OS on virtualized hardware. Containers are usually lighter, but the isolation models differ.

### 7. What is Docker Engine?
The runtime/service that manages Docker objects and container operations.

### 8. What is a registry?
A service that stores and distributes images.

### 9. What is Docker Hub?
A registry service for public and private container images.

### 10. What is a Dockerfile?
A text file containing instructions for building an image.

### 11. What is a layer?
A filesystem change that contributes to an image and may be reused by other builds.

### 12. What is a tag?
A label used to reference an image version, e.g. `booking-app:1.0`.

### 13. What is a container's writable layer?
The container-specific writable filesystem layer. It is normally removed with the container, so important data should live in a volume or external service.

### 14. What is Docker Compose?
A tool for defining and running multi-container applications using a YAML file.

### 15. Does Docker automatically scale and orchestrate a cluster?
No. Docker can run multiple containers, but multi-host orchestration and production scaling may require Kubernetes or another orchestration platform.

## Commands and lifecycle

### 16. How do you list running containers?
`docker ps`.

### 17. How do you list stopped containers too?
`docker ps -a`.

### 18. How do you list images?
`docker images` or `docker image ls`.

### 19. How do you inspect logs?
`docker logs <container>`; use `-f` to follow logs.

### 20. How do you execute a shell in a running container?
`docker exec -it <container> sh`, if the image includes sh. Bash may not be installed.

### 21. Stop vs remove?
Stop halts the container; remove deletes the container object. Images and volumes are separate objects.

### 22. What does `--rm` do?
Automatically removes a container after it exits.

### 23. What does `-d` do?
Runs the container detached in the background.

### 24. What does `-p 8085:8080` mean?
Publishes host port 8085 to container port 8080.

### 25. What is `docker inspect` used for?
It returns detailed configuration and runtime metadata for an object.

## Dockerfile

### 26. Explain FROM, WORKDIR, COPY, RUN, CMD and ENTRYPOINT.
FROM selects a base image/stage; WORKDIR sets the working directory; COPY copies build-context files; RUN executes during build; CMD supplies a default command/arguments; ENTRYPOINT defines the main executable.

### 27. CMD vs ENTRYPOINT?
ENTRYPOINT defines the executable; CMD supplies defaults or arguments depending on the combination. Exec form generally handles process signals more predictably than shell form.

### 28. Does EXPOSE publish a port?
No. It documents the intended container port. Use `-p` or a network/proxy configuration to make the service reachable.

### 29. What is .dockerignore?
It excludes files from the build context, helping reduce build transfer, avoid unnecessary content and prevent accidental inclusion of local files.

### 30. What is a multi-stage build?
A Dockerfile with multiple stages, often using a builder stage and copying only runtime artifacts into the final image.

### 31. How do you reduce image size?
Use a suitable runtime image, multi-stage builds, .dockerignore, fewer unnecessary packages and only required runtime artifacts.

### 32. Why is layer caching useful?
Docker can reuse unchanged build steps, reducing build time. Order instructions so stable dependencies are processed before frequently changing source files where practical.

### 33. Why not put a password in ENV or ARG?
Build metadata, layers, logs or CI output may expose it. Use runtime secret management instead.

### 34. Why run as non-root?
It reduces privileges available to the application if compromised. Ensure file permissions and required capabilities are configured correctly.

## Networking and ports

### 35. Why does localhost fail between Compose containers?
Inside a container, localhost refers to that same container. Use the other service's DNS name and container port on a shared network.

### 36. Host port vs container port?
The host port is where traffic enters on the host; the container port is where the application listens inside the container.

### 37. How do containers discover each other in Compose?
Compose creates a project network and provides service-name DNS for services on that network.

### 38. Why might a Spring Boot service be unreachable despite port mapping?
Wrong port, app not listening, binding only to loopback, startup failure, firewall, incorrect protocol or network configuration.

### 39. What is a bridge network?
A common Docker network driver that connects containers on a host and can support container-to-container communication.

### 40. Should a database port be public?
Usually not unless there is a reviewed reason. Restrict access to required clients and networks.

## Volumes and storage

### 41. Why use a volume?
To persist data independently of a container's lifecycle.

### 42. Volume vs bind mount?
A volume is managed by Docker; a bind mount maps a specific host path.

### 43. Is a volume a backup?
No. It can be deleted or corrupted. Maintain and test backups for important data.

### 44. What does `docker compose down -v` do?
It removes Compose-managed named volumes as well as containers/network, potentially deleting database data.

### 45. Where should uploaded files go?
Often an object-storage service or a properly managed persistent storage solution, depending on architecture. Do not rely on a disposable container filesystem.

## Compose

### 46. What is compose.yaml?
The YAML file defining services, networks, volumes and related configuration.

### 47. What does `docker compose up -d --build` do?
Builds images as needed and starts the project in detached mode.

### 48. Does depends_on always wait for a database to be ready?
Not by itself in all forms. Use health checks where suitable and design the application to handle transient dependency failures.

### 49. How do you see Compose logs?
`docker compose logs -f` or `docker compose logs -f app`.

### 50. How do you validate Compose configuration?
`docker compose config`.

## Registry and CI/CD

### 51. Build vs pull?
Build creates an image from a build context; pull downloads an image from a registry.

### 52. Push vs pull?
Push uploads an image; pull downloads it.

### 53. How does Jenkins use Docker?
A pipeline can build/test the application, build and tag an image, scan it, authenticate securely, push it to a registry and trigger deployment.

### 54. Why avoid latest in production?
Its meaning can change. Explicit version tags or digests improve traceability and reproducibility.

### 55. How do you roll back?
Redeploy a previously verified image/configuration and verify health. Keep known-good artifacts and release metadata.

## Scenario-based questions

### 56. Container exits immediately. What do you do?
Use `docker ps -a`, inspect exit status and logs, then check command, JAR, Java version, configuration, permissions and dependency connectivity.

### 57. Spring Boot works locally but fails in Docker.
Check runtime Java compatibility, environment variables, filesystem paths, port binding, network names, dependencies and OS/architecture assumptions.

### 58. Spring Boot cannot connect to MySQL in Compose.
Use the MySQL service name and internal port, verify both services share a network, inspect credentials and DB readiness, and read app/DB logs.

### 59. MySQL data disappeared after container removal.
Mount the database data directory to a named volume or suitable persistent storage. Configure tested backups too.

### 60. Port 8080 is already in use.
Map a different host port, such as `-p 8085:8080`, or safely stop the conflicting process/container.

### 61. Image is huge.
Inspect image layers/history, use multi-stage builds, remove unnecessary packages, use a suitable runtime base and exclude irrelevant build context files.

### 62. Container repeatedly restarts.
Inspect logs, exit code, health status, memory/CPU, configuration, dependency readiness and restart policy. A restart policy is not a fix for the root cause.

### 63. Registry returns unauthorized.
Check repository visibility, image path/tag, authentication and permission scope. Use secure credentials.

### 64. S3/database credentials were committed into an image.
Rotate/revoke exposed credentials, audit access, remove them from future builds and Git history as appropriate, and switch to runtime secrets. Deleting the latest file alone is insufficient.

### 65. Application is slow under load.
Check resource metrics, JVM memory/GC, request latency, database performance, connection pools and downstream services. Do not assume more containers solve every bottleneck.

### 66. Health check is unhealthy, but the process is running.
Inspect the health-check command, path, port, timing, dependencies and logs. A running process is not the same as a healthy application.

### 67. Container has no shell or curl.
Minimal images may omit diagnostic tools. Use logs, inspect, exec with available binaries, or a separate approved diagnostic container on the same network.

### 68. How do you secure a production container?
Use trusted/patched images, least privilege, non-root execution, secret management, restricted ports, scanning, resource controls, logging and a patch/rollback plan.

### 69. Can two containers use the same container port?
Yes, because each has its own network namespace in normal configurations. If publishing to the same host interface, they cannot both bind the same host port simultaneously.

### 70. What is the difference between restart policy and orchestration?
A restart policy controls restart behavior for a container. An orchestrator manages workloads across a broader environment, including scheduling, scaling and rollout operations.

## Interview answer framework

For scenario questions, answer in this order:
1. Clarify the symptom and scope.
2. Check logs and runtime status.
3. Validate configuration and networking.
4. Form a likely hypothesis.
5. Test one cause at a time.
6. Apply a fix and verify.
7. Explain prevention (monitoring, tests, backups, security or deployment controls).

Do not claim production experience if you have only done a local lab. Explain what you actually built and what you understand conceptually.
