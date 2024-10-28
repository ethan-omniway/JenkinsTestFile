pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('your-dockerhub-credentials-id')
        IMAGE_NAME = "ghcr.io/omnitw/letcrm-api"
    }

    stages {
        stage("Login to DockerHub") {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS | docker login -u your-dockerhub-username --password-stdin'
                }
            }
        }

        stage("Fetch and Increment Version") {
            steps {
                script {
                    // 获取所有 Docker 标签并找到最新的递增版本
                    def latestTag = sh(
                        script: "docker pull --all-tags ${IMAGE_NAME} || true && docker images --format '{{.Tag}}' ${IMAGE_NAME} | grep -E '^[0-9]+\\.[0-9]+\\.[0-9]+$' | sort -V | tail -n 1",
                        returnStdout: true
                    ).trim()

                    // 如果没有任何版本，则初始化为1.0.0
                    if (!latestTag) {
                        latestTag = "1.0.0"
                    }

                    echo "Current latest version on Docker Hub: ${latestTag}"

                    // 将最新版本号分割成主版本、次版本和小版本并递增
                    def (major, minor, patch) = latestTag.tokenize('.').collect { it.toInteger() }
                    patch += 1  // 递增小版本
                    def newVersion = "${major}.${minor}.${patch}"
                    env.IMAGE_VERSION = newVersion
                    echo "New Docker image version: ${env.IMAGE_VERSION}"
                }
            }
        }

        stage("Build Docker Image") {
            steps {
                script {
                    echo "Building Docker image with tag: ${IMAGE_NAME}:${IMAGE_VERSION}"
                    sh "docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} ."
                }
            }
        }

        stage("Push Docker Image") {
            steps {
                script {
                    echo "Pushing Docker image to DockerHub with tag ${IMAGE_VERSION}"
                    sh "docker push ${IMAGE_NAME}:${IMAGE_VERSION}"
                }
            }
        }
    }

    post {
        success {
            echo "Docker image ${IMAGE_NAME}:${IMAGE_VERSION} built and pushed successfully."
        }
        failure {
            echo "Build or Deployment Failed"
        }
    }
}
