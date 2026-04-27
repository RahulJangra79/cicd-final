pipeline {
    agent any

    environment {
        IMAGE_NAME = "rahuljangra/my-app3"
        CONTAINER_NAME = "my-app-container"
    }

    stages {
        stage('Clone Git Repository') {
            steps {
                git branch: 'main',
                url: 'https://github.com/RahulJangra79/cicd-final.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%BUILD_NUMBER% .'
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'docker-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin'
                }
            }
        }

        stage('Push Image to Docker Hub') {
            steps {
                bat 'docker push %IMAGE_NAME%:%BUILD_NUMBER%'
            }
        }

        stage('Run Container After Successful Push') {
            steps {
                bat '''
                docker rm -f %CONTAINER_NAME% || exit 0
                docker run -d -p 3000:3000 --name %CONTAINER_NAME% %IMAGE_NAME%:%BUILD_NUMBER%
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }
        failure {
            echo 'Pipeline failed.'
        }
    }
}
