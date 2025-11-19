pipeline {
    agent any

    environment {
        REGISTRY = "jayanthim/stateless-app"
        IMAGE_TAG = "${GIT_COMMIT}"
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
                withCredentials([usernamePassword(credentialsId: 'dockerhub-pass', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                        echo $PASS | docker login -u $USER --password-stdin
                        docker push $REGISTRY:$IMAGE_TAG
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                    # Replace image in deployment with new tag
                    kubectl set image deployment/stateless-deploy stateless-container=$REGISTRY:$IMAGE_TAG
                    kubectl apply -f k8s/service.yml
                """
            }
        }
    }

    triggers {
        pollSCM('*/2 * * * *')
    }
}
