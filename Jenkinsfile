pipeline {
    agent any 

    tools {
        nodejs 'node latest'  // 這裡填寫你在 Jenkins 中設置的 Node.js 名稱
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




        stage('Start Application') {
            steps {
                echo 'Starting the Express.js application using PM2...'
                sh 'pm2 start npm --name "express-app" -- start'  // 使用 PM2 啟動應用
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
