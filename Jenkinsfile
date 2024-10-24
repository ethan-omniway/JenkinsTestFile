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

    stage('Checkout') {
        steps {
            git branch: 'main', url: 'https://github.com/your_username/your_repo.git'
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

