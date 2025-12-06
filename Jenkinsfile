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
          def COMMIT = sh(script: "git rev-parse --short HEAD", returnStdout: true).trim()
          def TAG = "${IMAGE}:${COMMIT}"
          def LATEST = "${IMAGE}:latest"
          env.TAG = TAG
          sh "docker build -t ${TAG} ."
          sh "docker tag ${TAG} ${LATEST}"
        }
      }
    }

    stage('Deploy on same VM') {
      steps {
        script {
          sh '''
            docker rm -f simple-app || true
            # No docker pull needed, use local image
            docker run -d --name simple-app -p 3000:3000 --restart unless-stopped ${TAG}
          '''
        }
      }
    }
  }
}
