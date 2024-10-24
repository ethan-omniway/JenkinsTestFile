pipeline {
    agent any

    tools {
        nodejs 'node latest'
    }

    environment {
        NODE_ENV = 'production'
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
    }

    // 在 script 外定义 randomImageName 作为全局变量
    def randomImageName = ""

    stages {
        stage('Install Dependencies') {  
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }

        stage('Run Tests') {
            steps {
                echo 'Running tests....'
                sh 'npm test'
            }
        }

        stage('Build Random Docker Image') {
            steps {
                script {
                    // 使用随机名称构建 Docker 镜像，并保存为全局变量
                    randomImageName = "ghcr.io/ethan-omniway/random-image:${java.util.UUID.randomUUID()}"
                    echo "Building Docker image with name: ${randomImageName}"

                    // 构建镜像
                    sh "docker build -t ${randomImageName} ."
                }
            }
        }

        stage('Login to GitHub Container Registry') {
            steps {
                script {
                    // 登录 GitHub Container Registry
                    sh 'echo $DOCKERHUB_CREDENTIALS | docker login ghcr.io -u ethan-omniway --password-stdin'
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // 推送镜像到 GitHub Packages
                    sh "docker push ${randomImageName}"
                }
            }
        }
    }

    post {
        success {
            echo "Build and deployment of ${randomImageName} successful!"
        }
        failure {
            echo 'Build or deployment failed.'
        }
    }
}
