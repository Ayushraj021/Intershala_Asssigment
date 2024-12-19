## README.md

### Automated CI/CD Pipeline with Jenkins, Docker, and Kubernetes

This project automates the CI/CD pipeline for a ReactJS application, including testing, Docker image creation, Kubernetes deployment, and Slack notifications for build success or failure.

---

### Prerequisites

1. **Launch and Connect to EC2 Server**  
   - Ensure an EC2 instance is launched and SSH into it.

2. **Install Required Tools**  
   - Install **Docker**, **Jenkins**, and **Kops** on the EC2 instance.

3. **Setup Docker Hub**  
   - Create a Docker Hub repository.
   - Login to Docker Hub from the EC2 instance.

4. **ReactJS Application Docker Image**  
   - Build a Docker image of the ReactJS app.
   - Push the Docker image to Docker Hub.

5. **Kubernetes Manifest File**  
   - Create a Kubernetes deployment manifest for the ReactJS application.

6. **Jenkins Setup**  
   - Open Jenkins and install the required plugins:
     - **Docker Pipeline**
     - **Kubernetes CLI**
     - **Slack Notification**
   - Configure the pipeline as per the provided `Jenkinsfile`.

7. **Slack Integration**  
   - Configure Slack notifications for deployment status.

---

### Steps to Implement

1. **Set up the EC2 Server**  
   Launch an EC2 instance and install Docker, Jenkins, and Kops using the commands below:

   ```bash
   sudo apt update
   sudo apt install docker.io -y
   sudo apt install openjdk-11-jdk -y
   curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee \
       /usr/share/keyrings/jenkins-keyring.asc > /dev/null
   echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] \
       https://pkg.jenkins.io/debian-stable binary/ | sudo tee \
       /etc/apt/sources.list.d/jenkins.list > /dev/null
   sudo apt update
   sudo apt install jenkins -y
   ```

   Install Kops:

   ```bash
   curl -Lo kops https://github.com/kubernetes/kops/releases/download/v1.23.0/kops-linux-amd64
   chmod +x kops
   sudo mv kops /usr/local/bin/
   ```

2. **ReactJS Docker Image**  
   Build and push the ReactJS Docker image to Docker Hub:

   ```bash
   docker build -t ayushraj021/ubuntu .
   docker login
   docker push ayushraj021/ubuntu
   ```

3. **Kubernetes Deployment**  
   Use the following `manifest.yaml` file to deploy the ReactJS app:

   ```yaml
   apiVersion: apps/v1
   kind: Deployment
   metadata:
     name: react-app
   spec:
     replicas: 2
     selector:
       matchLabels:
         app: react-app
     template:
       metadata:
         labels:
           app: react-app
       spec:
         containers:
         - name: react-app
           image: ayushraj021/ubuntu
           ports:
           - containerPort: 3000
   ```

   Apply the manifest:

   ```bash
   kubectl apply -f manifest.yaml
   ```

4. **Jenkins Pipeline**  
   Configure the Jenkins pipeline with the provided `Jenkinsfile`.

5. **Slack Notifications**  
   Add Slack credentials to Jenkins and configure Slack notification settings for deployment status.

---

### Pipeline Overview

- **Stages**:
  1. **Checkout**: Clones the repository from GitHub.
  2. **Install Dependencies**: Installs required packages using `npm`.
  3. **Run Tests**: Runs the test suite to ensure the code is error-free.
  4. **Build Docker Image**: Builds a Docker image for the ReactJS app.
  5. **Push Docker Image**: Pushes the Docker image to Docker Hub.
  6. **Deploy to Kubernetes**: Updates the Kubernetes deployment with the new image.

- **Post-Deployment Notifications**:  
  - Sends success or failure notifications to the specified Slack channel.

---

### Key Commands

- **Run Pipeline**: Initiate the Jenkins pipeline.
- **Monitor Kubernetes Deployment**: Check the status:

  ```bash
  kubectl get pods
  kubectl rollout status deployment/react-app
  ```

- **Slack Notification**: View notifications in the configured Slack channel.

---

### Result

- The pipeline ensures:
  1. Automatic testing and deployment.
  2. Deployment to a Kubernetes cluster with high availability.
  3. Real-time Slack notifications for deployment status.