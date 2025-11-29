pipeline {
  agent any

  environment {
    IMAGE_NAME = "jonathan661/flask-jenkins"   
    DOCKERHUB_CREDS = 'dockerhub-creds'                
    APP_CONTAINER_NAME = 'flask-app'
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
          // use build number as tag
          def tag = "${env.BUILD_NUMBER}"
          sh "docker build -t ${env.IMAGE_NAME}:${tag} ."
        }
      }
    }

    stage('Push to DockerHub') {
      steps {
        withCredentials([usernamePassword(credentialsId: "${env.DOCKERHUB_CREDS}", usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
          sh '''
            echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
            docker tag ${IMAGE_NAME}:${BUILD_NUMBER} ${IMAGE_NAME}:latest
            docker push ${IMAGE_NAME}:${BUILD_NUMBER}
            docker push ${IMAGE_NAME}:latest
          '''
        }
      }
    }

    stage('Deploy to Server') {
      steps {
        // Use SSH credentials to SSH into APP_SERVER and update container
        sshagent (credentials: ["${env.SSH_CREDENTIALS_ID}"]) {
          sh """
           ssh -o StrictHostKeyChecking=no ${APP_SERVER} '
             set -e
             echo "[deploy] Pulling image ${IMAGE_NAME}:${BUILD_NUMBER}"
             docker pull ${IMAGE_NAME}:${BUILD_NUMBER} || docker pull ${IMAGE_NAME}:latest
             docker stop ${APP_CONTAINER_NAME} || true
             docker rm ${APP_CONTAINER_NAME} || true
             docker run -d --name ${APP_CONTAINER_NAME} -p 5000:5000 ${IMAGE_NAME}:${BUILD_NUMBER}
           '
          """
        }
      }
    }
  }

  post {
    success { echo "Pipeline succeeded." }
    failure { echo "Pipeline failed." }
  }
}


