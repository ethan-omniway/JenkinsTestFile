pipeline {
    agent any

    environment {
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
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
                    // 使用 Docker Hub API 获取版本列表并找到最新版本
                    def imageName = env.IMAGE_NAME.split('/')[1] // 取出仓库名称部分
                    def tagsResponse = sh(
                        script: "curl -s https://hub.docker.com/v2/repositories/${env.IMAGE_NAME}/tags/",
                        returnStdout: true
                    ).trim()
                    def tags = readJSON text: tagsResponse

                    // 筛选符合 0.x 格式的标签
                    def latestTag = tags.results.findAll { tag ->
                        tag.name ==~ /^0\.\d+$/
                    }.collect { it.name }
                    .sort { a, b -> 
                        def aVersion = a.tokenize('.').collect { it.toInteger() }
                        def bVersion = b.tokenize('.').collect { it.toInteger() }
                        return aVersion[1] <=> bVersion[1]
                    }.last() ?: "0.1"

                    echo "Current latest version on Docker Hub: ${latestTag}"

                    // 将版本递增
                    def (major, minor) = latestTag.tokenize('.').collect { it.toInteger() }
                    minor += 1
                    def newVersion = "${major}.${minor}"
                    env.IMAGE_VERSION = newVersion
                    echo "New Docker image version: ${env.IMAGE_VERSION}"
                }
            }
        }

        // stage("Build Docker Image") {
        //     steps {
        //         script {
        //             echo "Building Docker image with tag: ${IMAGE_NAME}:${IMAGE_VERSION}"
        //             sh "docker build -t ${IMAGE_NAME}:${IMAGE_VERSION} ."
        //         }
        //     }
        // }

        // stage("Push Docker Image") {
        //     steps {
        //         script {
        //             echo "Pushing Docker image to DockerHub with tag ${IMAGE_VERSION}"
        //             sh "docker push ${IMAGE_NAME}:${IMAGE_VERSION}"
        //         }
        //     }
        // }
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
