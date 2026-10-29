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

## Additional current-version, build and operations questions

### 71. Is Docker Desktop version the same as Docker Engine version?
No. Docker Desktop is the Windows/macOS application and packages components including an Engine, CLI and Compose. Docker Engine is the container runtime/daemon. Check both client and server details with `docker version`.

### 72. What Docker version should I install?
Use the latest stable Docker Desktop release offered for your supported Windows system, after checking its official release notes and requirements. Avoid preview/test builds for a normal learning environment unless you specifically need to test them.

### 73. What was the latest Docker Desktop release checked for this guide?
On 29 October 2026, Docker's official release-notes page listed Docker Desktop 4.97.0, dated 26 October 2026. Updates roll out gradually, so a particular computer may not receive it immediately. Check the live official page rather than treating this note as permanently current.

### 74. How do I update Docker Desktop safely?
Check release notes, save work, back up important data, update through Docker Desktop or the official Windows installer, restart if requested, then verify `docker version`, `docker compose version`, and a test container.

### 75. Is `docker-compose` the same as `docker compose`?
`docker compose` is the modern Compose CLI plugin. `docker-compose` with a hyphen refers to the older standalone v1 command in many tutorials. Use the modern plugin for new learning unless a specific legacy environment requires otherwise.

### 76. What is BuildKit?
BuildKit is Docker's modern build backend. It supports efficient caching, parallel build steps, improved build features and secret/SSH mounts. Use current Docker build tooling and consult the Dockerfile/build documentation for supported syntax.

### 77. What is Buildx?
Buildx is a Docker CLI plugin that provides extended build capabilities, including BuildKit features and multi-platform builds. Check it with `docker buildx version`.

### 78. What is a multi-platform image?
An image reference that can provide platform-specific image variants, commonly for Linux AMD64 and ARM64. Buildx can build/publish multi-platform images when the builder and base images support them.

### 79. What is an image digest?
A content-addressed identifier for an image manifest or index. Tags can move; a digest identifies specific content and can improve deployment reproducibility.

### 80. What is the difference between a tag and a digest?
A tag is a human-readable name that may be reassigned. A digest is derived from content and is immutable for that content. Production deployments may pin digests when strict reproducibility is required.

### 81. What is OCI?
The Open Container Initiative defines open standards around container image formats, runtimes and distribution. OCI compatibility helps tools exchange container artifacts.

### 82. What is containerd?
A container runtime component used by Docker Engine to manage container lifecycle and related operations. It is one component in the overall Docker architecture, not a synonym for the Docker CLI or Docker Desktop.

### 83. What is a health check?
A configured test that reports whether a containerized service appears healthy. A running process may still be unhealthy; ensure the test matches the application's actual readiness and available tools.

### 84. What is the difference between liveness and readiness?
Liveness asks whether the process should remain running or be restarted; readiness asks whether it is ready to serve traffic. Docker health checks are not automatically a full orchestration readiness/liveness system.

### 85. What is a restart policy?
A rule such as `no`, `on-failure`, `always` or `unless-stopped` that controls when Docker restarts a container. It does not diagnose the root cause of a crash.

### 86. What is rootless Docker?
A mode where the daemon and containers run without root privileges, reducing certain risks. Availability and setup depend on the platform; do not assume Docker Desktop on Windows uses the same setup as a Linux rootless Engine.

### 87. What is the Docker socket and why protect it?
The Docker API socket grants powerful control over the daemon. Access can effectively provide host-level control in many configurations, so never expose it casually or mount it into untrusted containers.

### 88. What is image scanning?
Analyzing an image for known vulnerabilities and related issues. Scanning helps but does not prove an image is secure; keep dependencies patched and review findings in context.

### 89. What is a software bill of materials (SBOM)?
An inventory of software components in an artifact. It supports dependency visibility and vulnerability response. Use currently supported Docker or ecosystem tooling; commands can change across releases.

### 90. How should secrets be passed to a container?
Use an appropriate secret manager or deployment platform's secret mechanism. Avoid committing secrets to Git, baking them into image layers, or exposing them in logs. Environment variables can also leak through process inspection or diagnostics, so evaluate the threat model.

### 91. Why should I pin base-image versions?
Pinning a deliberate version improves repeatability and reviewability. Balance this with a regular update process so security fixes are not missed; digest pinning gives stronger immutability.

### 92. How do you troubleshoot a build that cannot download dependencies?
Check network/DNS/proxy settings, registry or package repository status, credentials, certificates, build logs and cache behavior. Retry only after identifying whether the issue is transient or configuration-related.

### 93. Why use a non-root user in a Spring Boot image?
It limits the privileges of the application process. Ensure the JAR, temporary directory and any writable paths have correct ownership and permissions.

### 94. How do you keep a Docker image reproducible?
Use a controlled build context, lock/pin dependencies where practical, pin base images, avoid mutable downloads, record image digests, and build/test through a consistent CI pipeline.

### 95. How do you reduce attack surface?
Use maintained minimal images, remove unnecessary packages, run as non-root, drop unneeded capabilities, avoid privileged mode, restrict network exposure, scan images and manage secrets safely.

### 96. What is the difference between `docker stop` and `docker kill`?
`docker stop` requests graceful shutdown and then forcibly terminates after a timeout. `docker kill` sends a signal (SIGKILL by default) immediately. Prefer graceful stop for stateful workloads.

### 97. What is the difference between `docker rm` and `docker rmi`?
`docker rm` removes containers; `docker image rm` (often called `docker rmi`) removes image references/layers when no longer in use. Neither is a substitute for deliberate volume management.

### 98. Why is `docker system prune` risky?
It removes unused Docker resources, and options such as `-a` or `--volumes` broaden what can be removed. Inspect disk usage first and read the exact command's help. Never run destructive cleanup commands blindly on a machine with important data.

### 99. What should be checked after a Docker Desktop update?
Verify client/server versions, Compose and Buildx versions, context, Engine health, required containers, published ports, mounted volumes and application logs. Run a small test container and validate important projects.

### 100. Is Docker Desktop suitable for production servers?
Docker Desktop is primarily a desktop development tool. For Linux servers, follow the official Docker Engine installation guidance for the supported distribution and your organization's security/operations requirements.

### 101. Can every Docker interview question be listed?
No. Interviewers can create unlimited variations. Learn the underlying concepts, commands, debugging workflow and trade-offs rather than memorizing a supposedly exhaustive list.

