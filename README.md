# Jenkins Test

This project is a simple example for practicing Declarative Pipelines in Jenkins.

It includes a small Node.js function and a unit test with Jest.

---

## 🚀 Prerequisites

Before you begin, make sure you have:

- [Docker Desktop](https://www.docker.com/products/docker-desktop) installed.
- Access to [Jenkins](https://www.jenkins.io/) running in a container with permissions to use Docker.

Example of how to launch Jenkins with Docker and enable access to Docker within the container:

```bash
docker run -d \
  --name jenkins \
  -u root \
  -p 8080:8080 -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  -v /var/run/docker.sock:/var/run/docker.sock \
  jenkins/jenkins:lts
```

Enter the container and add Docker:

```bash
docker exec -it jenkins bash
```

Inside the container:

```bash
apt-get update
apt-get install -y docker.io
```

Exit the container (`exit`) and that's it. Jenkins will now have the `docker` binary.

This will allow Jenkins to be accessed at:

👉 http://localhost:8080

## ⚙️ Configuration in Jenkins UI

⚠️ **Important**: The pipeline script is not in the repository.
It must be copied directly into the Jenkins UI.

### Steps:

1. Log in to Jenkins: http://localhost:8080
2. Go to **New Item**.
3. Create a **Pipeline** project (e.g., Test-Pipeline).
4. Go to the project settings.
5. Scroll down to the **Pipeline** section.
6. Select **Pipeline script** (not "Pipeline script from SCM").
7. Paste the following script:

```groovy
pipeline {
    agent any
    stages {
        stage('Clone repo') {
            steps {
                cleanWs()
                git branch: 'main', url: 'https://github.com/danielureche/jenkins-test.git'
            }
        }
        stage('Build & Test in Node') {
            steps {
                script {
                    docker.image('node:18').inside {
                        sh 'npm install'
                        sh 'npm test'
                    }
                }
            }
        }
    }
    post {
        failure {
            echo '❌ The build failed. Check logs.'
        }
        success {
            echo '✅ Successful build!'
        }
    }
}
```

## 📌 Important Notes

- This pipeline is written in **Declarative Jenkins**, which is the recommended way to define pipelines in a clear and structured way.
- The repository contains only sample code with unit tests.
- The pipeline is managed from the Jenkins UI, allowing it to be modified without changing the repo code.
- The official Node.js 18 Docker image is used to run the tests.