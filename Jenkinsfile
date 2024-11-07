pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
        ORG_NAME = "omnitw"
        PACKAGE_NAME = "letcrm-api"
        NEW_VERSION = ""
    }

    stages {
        stage("Fetch New Package Versions") {
            steps {
                script {
                    // 使用 GitHub API 抓取指定 package 的版本列表
                    def response = sh(
                        script: """
                        curl -s -H "Authorization: Bearer $GITHUB_TOKEN" \
                        "https://api.github.com/orgs/$ORG_NAME/packages/container/$PACKAGE_NAME/versions"
                        """,
                        returnStdout: true
                    ).trim()

                    // 輸出 JSON 回應，便於除錯
                    echo "Response: ${response}"


                    // 解析 JSON 結果
                    def versions = readJSON text: response
                    def latestVersion = versions[0]?.metadata?.container?.tags[0]
                    echo "Latest version: ${latestVersion}"

                    // 分解版本號，遞增小版本
                    def (major, minor) = latestVersion.tokenize('.').collect { it.toInteger() }
                    minor += 1
                    NEW_VERSION = "${major}.${minor}"
                    echo "New version to be pushed: ${NEW_VERSION}"
                }
            }
        }

        stage("Build and Push New Version") {
            steps {
                script {
                    echo "Building Docker image with tag: ${ORG_NAME}/${PACKAGE_NAME}:${NEW_VERSION}"

                    sh "docker build -t letcrm-api:latest ."
                    sh "docker tag letcrm-api:latest ghcr.io/omnitw/letcrm-api:${NEW_VERSION}"

                    //login to ghcr.io
                    sh "echo $GITHUB_TOKEN | docker login ghcr.io -u ethan-omniway --password-stdin"

                    //push to ghcr.io
                    sh "docker push ghcr.io/omnitw/letcrm-api:${NEW_VERSION}"
                }
            }
        }
    }

    post {
        success {
            echo "New Docker image version ${NEW_VERSION} built and pushed successfully."
        }
        failure {
            echo "Build or Deployment Failed"
        }
    }
}
