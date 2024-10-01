pipeline {
    agent any

    triggers {
        pollSCM('H/5 * * * *') // 每 5 分鐘進行一次輪詢檢查
    }

    stages {
        stage('Build') {
            steps {
                sh 'echo "Building..."'
            }
        }

        stage('Test') {
            steps {
                sh 'echo "Testing..."'
            }
        }

        stage('Deploy') {
            steps {
                sh 'echo "Deploying..."'
            }
        }
    }
}
