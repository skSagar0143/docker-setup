# 9. Practical Labs and Short Notes

Complete the labs in order. Use disposable test data and avoid deleting volumes that contain data you need.

## Lab 1: Verify Docker

```bash
docker --version
docker info
docker run --rm hello-world
```

Expected result: Docker CLI and Engine are available and the test image can run.

## Lab 2: Run a web container

```bash
docker run -d --name nginx-lab -p 8085:80 nginx:stable
docker ps
docker logs nginx-lab
```

Open http://localhost:8085. Then:

```bash
docker stop nginx-lab
docker start nginx-lab
docker rm -f nginx-lab
```

## Lab 3: Containerize Spring Boot

1. Build a runnable JAR using `mvn clean package`.
2. Confirm its exact filename in `target/`.
3. Create a Dockerfile using [the Spring Boot guide](04-dockerfile-spring-boot.md).
4. Build:

```bash
docker build -t booking-app:1.0 .
```

5. Run:

```bash
docker run -d --name booking-app -p 8080:8080 booking-app:1.0
docker logs -f booking-app
```

6. Test a real endpoint from your application using Postman.
7. Stop/remove the container after testing.

## Lab 4: Compose and MySQL

Follow the complete example in [Compose, networking and volumes](05-compose-networking-volumes.md). Confirm:
- `docker compose config` succeeds.
- Both services are running/healthy.
- Spring Boot uses `mysql:3306`, not `localhost:3306`, for the Compose database.
- Data is mounted to a named volume.
- `.env` is not committed.

## Lab 5: Registry practice

Use a non-sensitive lab image and a registry repository you control. Build, tag, log in, push, and pull. Confirm repository visibility before publishing.

## Lab 6: Troubleshooting drill

Intentionally change a disposable lab configuration to cause one failure at a time:
- Use an incorrect host port.
- Use a wrong Compose service hostname.
- Use a wrong application profile.
- Remove the required JAR from the build context.
Then diagnose using `docker ps -a`, `docker logs`, `docker inspect` and `docker compose config`. Restore the working configuration afterward.

## One-page short notes

- **Docker:** build, distribute and run applications in containers.
- **Image:** reusable layered template.
- **Container:** runtime instance of an image.
- **Dockerfile:** image build instructions.
- **Registry:** stores/distributes images.
- **Compose:** defines multi-container applications.
- **Port syntax:** `-p HOST:CONTAINER`.
- **Service-to-service:** use Compose service DNS name and container port.
- **Persistent data:** named volume or suitable persistent storage.
- **Volume is not backup:** backups must be separate and tested.
- **EXPOSE:** documentation; does not publish a host port.
- **CMD/ENTRYPOINT:** default command/arguments vs main executable; exact interaction depends on form.
- **Multi-stage build:** build with tools, run from a smaller runtime image.
- **.dockerignore:** excludes files from build context.
- **Secrets:** never bake into images or source control.
- **Troubleshoot:** status -> logs -> config -> ports -> network -> storage -> resources.
- **CI/CD:** source -> build/tests -> image -> registry -> deploy -> verify/rollback.

## Final self-assessment

You are ready for basic Docker interview discussions when you can, without notes:
- Explain image/container/VM differences.
- Write and explain a Spring Boot Dockerfile.
- Build and run the image.
- Explain host vs container ports.
- Connect Compose services correctly.
- Persist data safely.
- Explain image tags and registry operations.
- Troubleshoot common startup/network/database failures.
- Explain image optimization, security and CI/CD.
- Answer scenario questions using evidence-based troubleshooting.
