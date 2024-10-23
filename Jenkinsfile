pipeline {
    agent any 

    tools {
        nodejs 'node latest'  // 確保這裡的名稱與 Jenkins 中設置的 Node.js 名稱一致
    }

    environment {
        NODE_ENV = 'production'  
    }

    stages {
        stage('Install Dependencies') {  // 安裝依賴
            steps {
                echo 'Installing dependencies...'
                sh 'npm install'  // 安裝 package.json 中的依賴
            }
        }

        stage('Run Tests') {  // 執行測試
            steps {
                echo 'Running tests....'
            }
        }

        stage('Start Application') {  // 啟動應用程序
            steps {
                echo 'Starting the Express.js application...'
                sh 'npm start'  // 啟動應用程序
            }
        }

        stage('Deploy') {  // 部署
            steps {
                echo 'Deploying the application...'
                // 部署代碼的步驟，例如上傳文件或執行部署腳本
            }
        }

        stage('Execute command over SSH') {  // 部署後執行 SSH 操作
            steps {
                sshPublisher(
                    publishers: [
                        sshPublisherDesc(
                            configName: 'ethan_test', // 你設定的 SSH 伺服器名稱
                            transfers: [
                                sshTransfer(
                                    execCommand: 'ls' // 這裡是你要執行的命令
                                )
                            ],
                            usePromotionTimestamp: false,
                            useWorkspaceInPromotion: false,
                            verbose: true
                        )
                    ]
                )
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
