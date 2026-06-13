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

        stage('Docker Login & Push (FIXED WINDOWS SAFE)') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat '''
                    echo ===== DEBUG INFO =====
                    echo USER=%DOCKER_USER%
                    echo ======================

                    REM Write password safely to file (Windows safe method)
                    echo %DOCKER_PASS%>dockerpass.txt

                    REM Login using file (NO PIPE ISSUES)
                    type dockerpass.txt | docker login -u %DOCKER_USER% --password-stdin

                    if %ERRORLEVEL% NEQ 0 exit /b 1

                    del dockerpass.txt

                    REM Push image
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
