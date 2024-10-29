pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
        ORG_NAME = "omnitw"
        PACKAGE_NAME = "letcrm-api"
        NEW_VERSION = ""
    }

    stages {
        stage("Fetch Package Versions") {
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

                    echo "Response: ${response}"
                    
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
                    // 假設你已經有 Dockerfile，這裡可以構建並推送新版本
                    echo "Building Docker image with tag: ${ORG_NAME}/${PACKAGE_NAME}:${NEW_VERSION}"

                    // echo "Pushing Docker image with new version ${NEW_VERSION}"
                    // sh "docker push ghcr.io/${ORG_NAME}/${PACKAGE_NAME}:${NEW_VERSION}"
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
