pipeline {
    agent any

    environment {
        IMAGE_NAME = "jatindixit/devops-static-website"
        IMAGE_TAG = "latest"
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                url: 'https://github.com/Jatindixit123/devops-k8s-jenkins-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:%IMAGE_TAG% .'
            }
        }

        stage('Docker Login & Push (Debug Enabled)') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat '''
                    echo ===== DEBUG INFO =====
                    echo USER=%DOCKER_USER%
                    echo PASS_LENGTH=%DOCKER_PASS%

                    echo ======================

                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin

                    if %ERRORLEVEL% NEQ 0 exit /b 1

                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    '''
                }
            }
        }

        stage('Deploy To Minikube') {
            steps {
                bat 'kubectl apply -f k8s/deployment.yaml'
                bat 'kubectl apply -f k8s/service.yaml'
            }
        }
    }
}