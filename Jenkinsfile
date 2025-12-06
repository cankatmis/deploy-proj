pipeline {
    agent any
    environment {
        IMAGE = "simple-app"
    }
    stages {
        stage('Checkout') {
            steps { git 'https://github.com/cankatmis/deploy-proj.git' }
        }
        stage('Build Docker') {
            steps { sh 'sudo docker build -t ${IMAGE} .' }
        }
        stage('Run Container') {
            steps { sh 'sudo docker rm -f simple-app || true; sudo docker run -d --name simple-app -p 3000:3000 ${IMAGE}' }
        }
    }
}
