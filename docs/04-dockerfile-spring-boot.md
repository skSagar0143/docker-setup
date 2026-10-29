# 4. Dockerfile and Spring Boot Containerization

## Prerequisites

- A Spring Boot project that builds a runnable JAR.
- Java and Maven installed for the local build, or a build stage in the Dockerfile.
- Docker Desktop running.
- Application port known (8080 in these examples).

## Option A: Dockerfile for an already-built JAR

Assume Maven produces `target/booking-app.jar`. Create a file named exactly `Dockerfile` (no extension) in the project root:

```dockerfile
FROM eclipse-temurin:21-jre
WORKDIR /app
COPY target/booking-app.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

Use a Java runtime compatible with the project's build. Confirm the exact JAR name under `target/`; adjust the COPY instruction accordingly.

Build and run:

```bash
mvn clean package
docker build -t booking-app:1.0 .
docker run --rm -p 8080:8080 booking-app:1.0
```

If port 8080 is already in use on the host, use another host port, e.g. `-p 8085:8080`.

## Option B: Multi-stage Dockerfile

This compiles the application inside a build stage and copies the JAR to a runtime stage. Review the Maven project and artifact naming before using it.

```dockerfile
FROM maven:3.9-eclipse-temurin-21 AS build
WORKDIR /workspace
COPY pom.xml .
RUN mvn -B -q dependency:go-offline
COPY src ./src
RUN mvn -B -DskipTests package

FROM eclipse-temurin:21-jre
WORKDIR /app
COPY --from=build /workspace/target/*.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "app.jar"]
```

This is a teaching example. Some projects require additional files copied before Maven can resolve dependencies, and `target/*.jar` may match more than one JAR. In a production build, make the artifact selection deterministic and run tests in CI rather than blindly skipping them.

## Dockerfile instruction quick reference

- `FROM`: base image or build stage.
- `WORKDIR`: sets the working directory.
- `COPY`: copies files from the build context.
- `ADD`: copies files and has additional archive/URL behavior; prefer COPY for ordinary file copies.
- `RUN`: executes a command during image build.
- `ENV`: defines environment variables in the image; do not use it for secrets.
- `ARG`: build-time argument; not a secure secret store.
- `EXPOSE`: documents a port; does not publish it to the host.
- `CMD`: default command/arguments, depending on ENTRYPOINT.
- `ENTRYPOINT`: main executable for the container.
- `USER`: selects the runtime user.
- `HEALTHCHECK`: defines a container health check where supported and appropriate.
- `VOLUME`: declares a mount point; persistent storage behavior still depends on how the container is run.

## CMD vs ENTRYPOINT

Exec form is usually preferred for application processes:

```dockerfile
ENTRYPOINT ["java", "-jar", "app.jar"]
```

ENTRYPOINT defines the main executable; CMD provides defaults or arguments depending on how the two are combined. Shell form may affect signal handling because a shell can become the parent process.

## .dockerignore

Create `.dockerignore` in the build context root, for example:

```text
.git
.gitignore
.idea
.vscode
target
*.log
.env
.env.*
secrets/
keys/
```

Only exclude files that the build does not need. If your Dockerfile copies a JAR from `target/`, do not exclude target unless you change the workflow to build the JAR inside a multi-stage build. For the multi-stage example, excluding local `target/` is generally fine.

Never commit real secrets. Excluding a file from Docker's build context does not remove it from Git history if it was already committed.

## Runtime configuration

Prefer runtime environment variables for environment-specific configuration. Spring Boot commonly maps variables such as `SPRING_DATASOURCE_URL` to application properties.

Example:

```bash
docker run --rm -p 8080:8080 \
  -e SPRING_PROFILES_ACTIVE=dev \
  -e SERVER_PORT=8080 \
  booking-app:1.0
```

Do not bake database passwords or API keys into the image.

## Port and health checks

`EXPOSE 8080` documents the expected container port. Publish it with `-p 8080:8080` when host access is needed. Ensure Spring Boot listens on the intended port and interface.

Health-check design depends on the application and base image. A Docker HEALTHCHECK is not automatically equivalent to Spring Boot Actuator readiness/liveness probes. Avoid adding a health-check command that depends on a utility absent from the image.

## Image size and build performance

- Use an appropriate runtime base image.
- Use multi-stage builds to exclude build tools from runtime.
- Copy dependency manifests before frequently changing source files where this improves layer cache reuse.
- Use `.dockerignore`.
- Avoid installing unnecessary OS packages.
- Use a deterministic artifact path.
- Scan, update and rebuild base images regularly.

## Common errors

### COPY failed / file not found
Check the build context, Dockerfile location, exact JAR filename and .dockerignore rules.

### No main manifest attribute
The JAR may not be an executable Spring Boot JAR. Check Maven packaging and the Spring Boot Maven plugin.

### Unsupported class version
The runtime Java version may be older than the version used to compile the application. Use a compatible runtime.

### Container exits after startup
Read logs and inspect the exit code. The main application process may have failed, finished, or been misconfigured.

### Host port already allocated
Choose another host port, such as `-p 8085:8080`, or free the conflicting port.

### Database connection refused
Check hostname, port, network, credentials, database readiness and application configuration. In Compose, use the database service name, not localhost, for service-to-service access.
