
pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe') // 在 Jenkins 中配置 GitHub Token 凭证
        IMAGE_NAME = "ghcr.io/omnitw/letcrm-api"
        PACKAGE_NAME = "letcrm-api"
        ORG_NAME = "omnitw"

    }

    stages {
        stage("Fetch and Increment Version") {
            steps {
                script {
                    // 登录到 GitHub Container Registry
                    sh 'echo $GITHUB_TOKEN | docker login ghcr.io -u ethan-omniway --password-stdin'
                    // 使用 GitHub API 抓取指定 package 的版本列表
                    def response = sh(
                        script: """
                        curl -s -H "Authorization: Bearer $GITHUB_TOKEN"
                        "https://api.github.com/orgs/$ORG_NAME/packages/container/$PACKAGE_NAME/versions"
                        """,
                        returnStdout: true
                    ).trim()
                    
                    // 將回應內容輸出至控制台（僅作為範例，實際應解析 JSON）
                    echo "GitHub API Response: ${response}"
                    
                    // 解析 JSON 格式的結果
                    def versions = readJSON text: response

                    // 獲取最新版本，假設按版本號排序
                    def latestVersion = versions[0]?.metadata?.container?.tags[0]
                    echo "Latest version: ${latestVersion}"
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




