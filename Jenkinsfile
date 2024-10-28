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
                    // 登录到 DockerHub
                    sh 'echo $DOCKERHUB_CREDENTIALS | docker login -u your-dockerhub-username --password-stdin'
                }
            }
        }

        stage("Fetch and Increment Version") {
            steps {
                script {
                    // 获取所有 Docker 标签并找到最新符合 0.x.0 格式的版本
                    def latestTag = sh(
                        script: "docker pull --all-tags ${IMAGE_NAME} || true && docker images --format '{{.Tag}}' ${IMAGE_NAME} | grep -E '^0\\.[0-9]+\\.0$' | sort -V | tail -n 1",
                        returnStdout: true
                    ).trim()

                    // 如果没有任何符合格式的版本，则初始化为 0.1.0
                    if (!latestTag) {
                        latestTag = "0.1.0"
                    }

                    echo "Current latest version on Docker Hub: ${latestTag}"

                    // 分割版本号，将中间版本号递增
                    def (major, minor, patch) = latestTag.tokenize('.').collect { it.toInteger() }
                    minor += 1  // 递增中间版本号
                    def newVersion = "${major}.${minor}.${patch}"
                    env.IMAGE_VERSION = newVersion
                    echo "New Docker image version: ${env.IMAGE_VERSION}"
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
