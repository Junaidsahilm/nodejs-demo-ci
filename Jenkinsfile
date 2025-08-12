pipeline {
    agent any
    environment {
        IMAGE_NAME = "my-node-app"
        DOCKER_HUB_REPO = "your-dockerhub-username/my-node-app"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }
        stage('Build') {
            steps {
                sh 'docker build -t $IMAGE_NAME .'
            }
        }
        stage('Test') {
            steps {
                sh 'npm install'
                sh 'npm test'
            }
        }
        stage('Push to DockerHub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker tag $IMAGE_NAME:latest $DOCKER_HUB_REPO:latest'
                    sh 'docker push $DOCKER_HUB_REPO:latest'
                }
            }
        }
        stage('Deploy') {
            steps {
                sh 'docker rm -f node-container || true'
                sh 'docker run -d --name node-container -p 3000:3000 $DOCKER_HUB_REPO:latest'
            }
        }
    }
}
