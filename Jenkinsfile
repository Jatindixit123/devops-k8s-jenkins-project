pipeline {
agent any

```
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

    stage('Docker Login & Push') {
        steps {
            withCredentials([usernamePassword(
                credentialsId: 'dockerhub-creds',
                usernameVariable: 'DOCKER_USER',
                passwordVariable: 'DOCKER_PASS'
            )]) {

                bat '''
                echo %DOCKER_PASS%>dockerpass.txt

                type dockerpass.txt | docker login -u %DOCKER_USER% --password-stdin

                if %ERRORLEVEL% NEQ 0 exit /b 1

                del dockerpass.txt

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

            kubectl apply -f k8s/deployment.yaml
            kubectl apply -f k8s/service.yaml

            kubectl rollout restart deployment/devops-static
            kubectl rollout status deployment/devops-static
            '''
        }
    }
}

post {
    success {
        echo 'Application deployed successfully'
    }

    failure {
        echo 'Pipeline failed'
    }
}


}
