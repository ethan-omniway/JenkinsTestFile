pipeline {
    agent any

    tools {
        git 'git' 
        nodejs 'node latest'
    }

    // triggers {
    //     githubPullRequests(events: [Open(), commitChanged()])
    // }

    environment {
        IMAGE_NAME = "ghcr.io/omnitw/letcrm-api"
        DOCKERHUB_CREDENTIALS = credentials('fcabbd2e-0256-4d82-be73-ca4017a805fe')
    }

    stages {
        stage("node & git version") {
            steps {
                echo 'Checking Node, Npm, and Docker versions'
                // setGitHubPullRequestStatus(context: 'Robot', message: 'Checking Node and Npm version', state: 'PENDING')
                sh '''
                    node -v
                    npm -v
                    git --version
                    docker --version
                '''
            }
        }


        stage('Login to GitHub Container Registry') {
            steps {
                script {
                    sh 'echo $DOCKERHUB_CREDENTIALS | docker login ghcr.io -u ethan-omniway --password-stdin'
                }
            }
        }



        stage("Pull Latest Docker Image") {
            steps {
                    script {
                        // 拉取最新的 Docker 映像
                        echo "Pulling latest Docker image: ${IMAGE_NAME}:latest"
                        sh "docker pull ${IMAGE_NAME}:latest || true"

                        // 獲取最新標籤
                        def latestTag = sh(
                            script: "docker images --format '{{.Tag}}' ${IMAGE_NAME} | sort -V | tail -n 1",
                            returnStdout: true
                        ).trim()

                        // 若沒有標籤則初始化版本
                        if (!latestTag) {
                            latestTag = "1.0.0"
                        }

                        // 解析並遞增版本號
                        def (major, minor, patch) = latestTag.tokenize('.').collect { it.toInteger() }
                        patch += 1  // 遞增小版本
                        env.IMAGE_VERSION = "${major}.${minor}.${patch}"
                        echo "New Docker image version: ${env.IMAGE_VERSION}"
                    }
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
            // cleanWs()
            echo 'Pipeline finished'
        }
        success {
            echo 'Build & Deployment Successful'
            // setGitHubPullRequestStatus(context: 'Robot', message: 'Jenkins Success', state: 'SUCCESS')
        }
        failure {
            echo 'Build or Deployment Failed'
            // setGitHubPullRequestStatus(context: 'Robot', message: 'Jenkins Failed', state: 'FAILURE')
        }
    }
}
