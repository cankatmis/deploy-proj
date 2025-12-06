pipeline {
  agent any
  environment {
    IMAGE = "cankatmis/simple-app"
  }
  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        script {
          COMMIT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          TAG = "${IMAGE}:${COMMIT}"
          LATEST = "${IMAGE}:latest"
          env.TAG = TAG
          sh "docker build -t ${TAG} ."
          sh "docker tag ${TAG} ${LATEST}"
        }
      }
    }

    stage('Push to Docker Hub') {
      steps {
        withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'USER', passwordVariable: 'PASS')]) {
          sh '''
            echo "$PASS" | docker login -u "$USER" --password-stdin
            docker push ${TAG}
            docker push ${IMAGE}:latest
            docker logout
          '''
        }
      }
    }

    stage('Deploy on same VM') {
      steps {
        script {
          sh '''
            docker rm -f simple-app || true
            docker pull ${TAG}
            docker run -d --name simple-app -p 3000:3000 --restart unless-stopped ${TAG}
          '''
        }
      }
    }
  }
}
