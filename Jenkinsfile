pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "kirthiksubbiah/flask-cicd"
        DOCKER_TAG = "${BUILD_NUMBER}"
    }

    stages {
        stage('Clone Repo') {
            steps {
                git branch: 'main', url: 'https://github.com/kirthiksubbiah/mock-test1.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    sh "docker build -t $DOCKER_IMAGE:$DOCKER_TAG ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
                    sh """
                    echo $PASS | docker login -u $USER --password-stdin
                    docker push $DOCKER_IMAGE:$DOCKER_TAG
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                sh """
                kubectl set image deployment/flask-deployment flask-container=$DOCKER_IMAGE:$DOCKER_TAG
                kubectl rollout status deployment/flask-deployment
                """
            }
        }
    }

    post {
        success {
            echo "✅ Deployment Successful!"
        }
        failure {
            echo "❌ Something went wrong."
        }
    }
}
