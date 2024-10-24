pipeline {
    agent any

    tools {
        nodejs 'node latest'
    }

    environment {
        NODE_ENV = 'production'
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
    }


    stages {
        stage('Install Dependencies') {  
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'
            }
        }


        stage('Build Nginx Docker Image') {
            steps {
                script {
                    // 拉取 Nginx 官方镜像并打标签
                    sh "docker pull nginx:latest"
                    sh "docker tag nginx:latest ghcr.io/ethan-omniway/nginx:latest"
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

        stage('Push Docker Image to GitHub Packages') {
            steps {
                script {
                    // 推送 Nginx 镜像到 GitHub Packages
                    sh "docker push ghcr.io/ethan-omniway/nginx:latest"
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
