pipeline {
  agent any
  environment {
    IMAGE = "hammad-app"
    CONTAINER = "hammad-container"
    PORT = "8080"
  }

  stages {
    stage('Checkout') {
      steps {
        checkout scm
      }
    }

    stage('Build Docker Image') {
      steps {
        sh '''
          echo "Building Docker image: ${IMAGE}:${BUILD_NUMBER}"
          docker build -t ${IMAGE}:${BUILD_NUMBER} -t ${IMAGE}:latest .
        '''
      }
    }

    stage('Stop & Remove old container') {
      steps {
        sh '''
          echo "Stopping/removing old container (if exists)..."
          if [ "$(docker ps -aq -f name=${CONTAINER})" ]; then
            docker stop ${CONTAINER} || true
            docker rm ${CONTAINER} || true
          else
            echo "No existing container named ${CONTAINER}"
          fi
        '''
      }
    }

    stage('Run new container') {
      steps {
        sh '''
          echo "Running new container..."
          docker run -d --restart=always -p ${PORT}:${PORT} --name ${CONTAINER} ${IMAGE}:${BUILD_NUMBER}
          sleep 2
          docker ps -f name=${CONTAINER}
        '''
      }
    }
  }

  post {
    always {
      sh 'echo "Deployment finished. Container status:"; docker ps -f name=${CONTAINER} || true'
    }
  }
}
