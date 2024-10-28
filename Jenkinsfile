pipeline {
    agent any

    tools {
        git 'git' 
        nodejs 'node latest'
    }

    triggers {
        githubPullRequests(events: [Open(), commitChanged()], spec: '', triggerMode: 'HEAVY_HOOKS')
    }

    environment {
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
    }

    stages {
        stage("node & git version") {
            steps {
                echo 'Checking Node and Npm version and Docker'
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
                echo "Running test code form develop"
            }
        }

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
        // stage('Build Nginx Docker Image') {
        //     steps {
        //         script {
        //             sh "docker pull nginx:latest"
        //             sh "docker tag nginx:latest ghcr.io/omnitw/nginx:0.1"
        //         }
        //     }
        // }

        // stage('Login to GitHub Container Registry') {
        //     steps {
        //         script {
        //             sh 'echo $DOCKERHUB_CREDENTIALS | docker login ghcr.io -u ethan-omniway --password-stdin'
        //         }
        //     }
        // }

        // stage('Push Docker Image to GitHub Packages') {
        //     steps {
        //         script {
        //             sh "docker push ghcr.io/omnitw/nginx:0.1"
        //         }
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
