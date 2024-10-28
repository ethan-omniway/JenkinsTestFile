pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe') // 在 Jenkins 中配置 GitHub Token 凭证
        IMAGE_NAME = "ghcr.io/omnitw/letcrm-api"
    }

    stages {
        stage("Fetch and Increment Version") {
            steps {
                script {
                    // 登录到 GitHub Container Registry
                    sh 'echo $GITHUB_TOKEN | docker login ghcr.io -u ethan-omniway --password-stdin'
                    
                    // 使用 GitHub API 获取标签列表
                    def response = sh(
                        script: """
                        curl -s -H "Authorization: Bearer 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N" \
                        "https://ghcr.io/v2/omnitw/letcrm-api/tags/list"
                        """,
                        returnStdout: true
                    ).trim()

                    // 确保输出标签列表
                    echo "GitHub API Response: ${response}"

                    // 解析标签信息
                    def tags = readJSON text: response

                    // 筛选符合 0.x 格式的标签
                    def latestTag = tags.tags.findAll { tag ->
                        tag ==~ /^0\.\d+$/
                    }.sort { a, b -> 
                        def aVersion = a.tokenize('.').collect { it.toInteger() }
                        def bVersion = b.tokenize('.').collect { it.toInteger() }
                        return aVersion[1] <=> bVersion[1]
                    }.last() ?: "0.1"

                    echo "Current latest version on GitHub Container Registry: ${latestTag}"

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
        //             echo "Pushing Docker image to GitHub Container Registry with tag ${IMAGE_VERSION}"
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
