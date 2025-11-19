pipeline {
    agent any

    environment {
        REGISTRY = "jayanthis952/stateless-app"
        IMAGE_TAG = "latest"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/jayanthis952/kubernetes-jenkins-stateless.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh "docker build -t $REGISTRY:$IMAGE_TAG ./app"
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([string(credentialsId: 'dockerhub-pass', variable: 'PASS')]) {
                    sh """
                        echo $PASS | docker login -u jayanthis952 --password-stdin
                        docker push $REGISTRY:$IMAGE_TAG
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    kubectl apply -f k8s/deployment.yaml
                    kubectl apply -f k8s/service.yaml
                """
            }
        }
    }

    triggers {
        pollSCM('*/2 * * * *')
    }
}

