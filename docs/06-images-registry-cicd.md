# 6. Images, Registries and CI/CD

## Image names and tags

An image reference commonly follows:

```text
[registry-host/][namespace/]repository[:tag]
```

Examples:
- `nginx:stable`
- `mysql:8.4`
- `username/booking-app:1.0`

A tag is a label, not a guarantee that an image is immutable. For tightly controlled production builds, consider digest pinning and a documented update process.

## Pull, build, tag and push

```bash
docker pull nginx:stable
docker build -t booking-app:1.0 .
docker tag booking-app:1.0 <dockerhub-username>/booking-app:1.0
docker login
docker push <dockerhub-username>/booking-app:1.0
docker pull <dockerhub-username>/booking-app:1.0
docker logout
```

Replace placeholders with your own registry namespace and repository. Do not publish private application images to a public repository by accident.

## What is Docker Hub?

Docker Hub is a registry service that hosts and distributes container images. Registries can be public or private, depending on the provider and repository configuration.

## How Docker fits into CI/CD

A typical workflow:

1. Developer pushes source code to GitHub.
2. CI server (e.g. Jenkins) checks out the code.
3. Maven compiles the project and runs tests.
4. CI builds a Docker image.
5. Image is tagged with a meaningful version or commit identifier.
6. CI authenticates using securely stored credentials.
7. CI pushes the image to a registry.
8. A deployment process pulls the approved image and runs it.
9. Health checks and monitoring verify the release.
10. A rollback plan is available if verification fails.

Do not deploy an image just because it built successfully. Include tests, review, security scanning and release controls appropriate to the project.

## Jenkins pipeline concepts

A declarative Jenkins pipeline commonly has stages such as Checkout, Build/Test, Build Image, Push Image and Deploy. Credentials should be managed through Jenkins credentials or a secure identity mechanism, not hardcoded in the Jenkinsfile.

## Versioning strategy

Useful tags include a release version (e.g. `1.4.2`) and a commit-based identifier. Avoid relying solely on `:latest` because its meaning can change over time and reduce deployment reproducibility.

## Rollback

Keep known-good image references and deployment configuration. Rollback means redeploying a previously verified version and checking health afterward; it is not simply deleting the newest image.

## Interview questions

**What is the difference between build and pull?** Build creates an image from a Dockerfile/build context; pull downloads an existing image from a registry.

**What does push do?** Uploads an image to a registry.

**Why tag images?** To identify versions and control which build is deployed.

**How should CI authenticate to a registry?** Use securely managed credentials or a supported identity mechanism, with minimum required permissions.

**How do you keep releases reproducible?** Pin versions where appropriate, build from reviewed source, use consistent build inputs, label artifacts and record the deployed image digest.
