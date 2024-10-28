pipeline {
    agent any

    tools {
        git 'git' 
        nodejs 'node latest'
    }

    triggers {
        githubPullRequests(events: [Open(), commitChanged()])
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
    }

    stages {
        stage("node & git version") {
            steps {
                echo 'Checking Node, Npm, and Docker versions'
                setGitHubPullRequestStatus(context: 'Robot', message: 'Checking Node and Npm version', state: 'PENDING')
                sh '''
                    node -v
                    npm -v
                    git --version
                    docker --version
                '''
            }
        }

        stage("test code"){
            steps {
                echo "Running test code from develop"
            }
        }

        // 如果需要，解開下列註解
        // stage('Clone Git Repository') {
        //     steps {
        //         echo 'Cloning the repository'
        //         setGitHubPullRequestStatus(context: 'Robot', message: 'Cloning the repository', state: 'PENDING')
        //         git(
        //             url: env.GIT_URL,
        //             branch: env.GITHUB_PR_SOURCE_BRANCH,
        //             credentialsId: '839fa9ee-f7d5-481e-8185-0f47d1566351'
        //         )
        //     }
        // }
    }

    post {
        always {
            cleanWs()
            echo 'Pipeline finished'
        }
        success {
            echo 'Build & Deployment Successful'
            setGitHubPullRequestStatus(context: 'Robot', message: 'Jenkins Success', state: 'SUCCESS')
        }
        failure {
            echo 'Build or Deployment Failed'
            setGitHubPullRequestStatus(context: 'Robot', message: 'Jenkins Failed', state: 'FAILURE')
        }
    }
}
