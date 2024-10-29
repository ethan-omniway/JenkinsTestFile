pipeline {
    agent any

    environment {
        GITHUB_TOKEN = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe') // GitHub Token 憑證 ID
        ORG_NAME = "omnitw" 
        PACKAGE_NAME = "letcrm-api"
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
                    
                    // 輸出 JSON 回應，便於除錯
                    echo "GitHub API Response: ${response}"
                    
                    // 解析 JSON 結果
                    def versions = readJSON text: response
                    def latestVersion = versions[0]?.metadata?.container?.tags[0]
                    echo "Latest version: ${latestVersion}"
                }
            }
        }
    }

    post {
        success {
            echo "Fetched package versions successfully."
        }
        failure {
            echo "Failed to fetch package versions."
        }
    }
}
