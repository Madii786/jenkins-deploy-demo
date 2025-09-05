pipeline {
    agent any

    environment {
        IMAGE_NAME = "hammad-app"
        CONTAINER_NAME = "hammad-container"
        PORT = "9090"   // host port (change if 9090 busy)
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/Madii786/jenkins-deploy-demo.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    def version = env.BUILD_NUMBER ?: 'local'
                    echo "Building Docker image: ${IMAGE_NAME}:${version}"
                    sh """
                        docker build -t ${IMAGE_NAME}:${version} -t ${IMAGE_NAME}:latest .
                    """
                }
            }
        }

        stage('Stop & Remove old container') {
            steps {
                sh '''
                  echo "Stopping/removing old container (if exists)..."
                  if [ "$(docker ps -aq -f name=$CONTAINER_NAME)" ]; then
                      docker rm -f $CONTAINER_NAME || true
                  else
                      echo "No existing container named $CONTAINER_NAME"
                  fi
                '''
            }
        }

        stage('Run new container') {
            steps {
                sh '''
                  echo "Running new container (host port ${PORT} -> container 8080)..."
                  docker run -d --restart=always -p ${PORT}:8080 --name $CONTAINER_NAME $IMAGE_NAME:latest
                '''
            }
        }
    }

    post {
        always {
            sh '''
              echo "=== Container status ==="
              docker ps -f name=$CONTAINER_NAME || true
              echo "=== Last 100 lines of container logs ==="
              docker logs --tail 100 $CONTAINER_NAME || true
            '''
        }
    }
}
