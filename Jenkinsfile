pipeline {
    agent any 

    tools {
        nodejs 'node latest'  // 确保你的 NodeJS 环境名称是 'node latest'
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

        stage('Run Tests') {  
            steps {
                echo 'Running tests....'
                // sh 'npm test'  // 执行 npm 测试
            }
        }

        stage('Build Random Docker Image') {
            steps {
                script {
                    // 使用随机名称构建 Docker 镜像
                    def randomImageName = "ghcr.io/ethan-omniway/random-image:${java.util.UUID.randomUUID()}"
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
            echo 'Build and Deployment successful!'
        }
        failure {
            echo 'Build or Deployment failed.'
        }
    }
}
