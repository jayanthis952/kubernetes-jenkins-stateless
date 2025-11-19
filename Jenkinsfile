pipeline {
    agent any

    environment {
        REGISTRY = "jayanthim/stateless-app"
    }

    stages {
        stage('Clone Repo') {
            steps {
                // Checkout the repo and get GIT_COMMIT
                git branch: 'main',
                    url: 'https://github.com/jayanthis952/kubernetes-jenkins-stateless.git'
                script {
                    env.IMAGE_TAG = sh(returnStdout: true, script: 'git rev-parse --short HEAD').trim()
                }
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
                    # Update deployment with new image
                    kubectl set image deployment/stateless-deploy stateless-app=$REGISTRY:$IMAGE_TAG
                    kubectl rollout status deployment/stateless-deploy
                    kubectl apply -f k8s/service.yml
                """
            }
        }
    }

    triggers {
        pollSCM('*/2 * * * *') // Poll Git every 2 minutes for changes
    }
}
