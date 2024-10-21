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




        stage('Start Application') {  // 啟動應用程序
            steps {
                echo 'Starting the Express.js application...'
                sh 'npm start'  // 使用 npm start 來啟動 Express.js 應用
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
