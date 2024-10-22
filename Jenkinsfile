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
                script {
                    // 在背景啟動應用
                    sh 'npm start &'
                    
                    // 等待 10 秒
                    sleep time: 10, unit: 'SECONDS'
                    
                    // 取得應用的 PID 並停止
                    sh 'pkill -f "npm start"'
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
