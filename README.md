# jenkins-nodejs
# Jenkins Node.js Docker Pipeline

This project demonstrates how to build a Docker image for a Node.js application, tag it, and push it to **DockerHub** using **Jenkins**. This pipeline automates the process of deploying Node.js applications in a Docker container, making it easier to manage and scale applications.

---

## Prerequisites

1. **Jenkins setup** with:
   - Docker installed on the Jenkins agent.
   - Jenkins user added to the `docker` group.

2. **DockerHub account** with:
   - Username (`DOCKER_USERNAME`)
   - Password or Access Token (`DOCKER_PASSWORD`)

3. **Jenkins Credentials**:
   - Store `DOCKER_USERNAME` and `DOCKER_PASSWORD` as Jenkins environment variables or credentials.

4. **Node.js app** with a `Dockerfile`.

---

## Steps

### 1. Build the Docker image

```bash
docker build . -t node-app:${BUILD_NUMBER}
```
`${BUILD_NUMBER}` is provided by Jenkins for every build. This creates a Docker image locally tagged as `node-app:<build_number>`.

### 2. Tag the image for DockerHub

```bash
docker tag node-app:${BUILD_NUMBER} ${DOCKER_USERNAME}/node-app:${BUILD_NUMBER}
```
Tags the image with your DockerHub username.

### 3. Login to DockerHub

```bash
echo "${DOCKER_PASSWORD}" | docker login -u "${DOCKER_USERNAME}" --password-stdin
```
Logs in securely using environment variables. Prevents exposing credentials in logs.

### 4. Push the image to DockerHub

```bash
docker push ${DOCKER_USERNAME}/node-app:${BUILD_NUMBER}
```
Pushes the image to DockerHub repository: `dockerhub.com/<DOCKER_USERNAME>/node-app:<BUILD_NUMBER>`.

---

## Example Jenkins Freestyle Job

Create a new Freestyle Project in Jenkins.

In Build Environment, add environment variables:

- `DOCKER_USERNAME=your-dockerhub-username`
- `DOCKER_PASSWORD=your-dockerhub-password-or-token`

Add an Execute shell step with the above commands.

Build the job — Jenkins will:

- Build the image
- Tag it
- Push it to DockerHub

### Verify the Image

After a successful build, run:

```bash
docker pull ${DOCKER_USERNAME}/node-app:<build_number>
docker run -p 3000:3000 ${DOCKER_USERNAME}/node-app:<build_number>
```
Your Node.js app will be available on [http://localhost:3000](http://localhost:3000).

---

## Troubleshooting

- **Issue**: Docker login fails.
  - **Solution**: Ensure that your DockerHub credentials are correct and that you have access to the DockerHub account.

- **Issue**: Image not found after pushing.
  - **Solution**: Verify that the image was tagged correctly and that the push command was executed without errors.

