pipeline {
    agent any

    environment {
        IMAGE_NAME = "jatindixit/devops-static-website"
        IMAGE_TAG = "${BUILD_NUMBER}"
        KUBECONFIG = "C:\\Users\\DELL\\.kube\\config"
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
                bat "docker build -t %IMAGE_NAME%:%IMAGE_TAG% ."
            }
        }

        stage('Docker Login & Push') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub-creds',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {

                    bat '''
                    echo %DOCKER_PASS%>pass.txt
                    type pass.txt | docker login -u %DOCKER_USER% --password-stdin
                    del pass.txt

                    docker push %IMAGE_NAME%:%IMAGE_TAG%
                    '''
                }
            }
        }

        stage('Deploy To Minikube') {
            steps {
                bat '''
                set KUBECONFIG=C:\\Users\\DELL\\.kube\\config

                kubectl config current-context
                kubectl get nodes

                REM Update image in deployment (IMPORTANT FIX)
                kubectl set image deployment/devops-static devops-static=%IMAGE_NAME%:%IMAGE_TAG%

                REM Wait for rollout
                kubectl rollout status deployment/devops-static

                REM Verify pods
                kubectl get pods
                '''
            }
        }
    }

    post {
        success {
            echo "SUCCESS: Application deployed successfully to Kubernetes"
        }

        failure {
            echo "FAILED: Pipeline execution failed"
        }
    }
}
