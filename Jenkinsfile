pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'rani2909/myjenkin' // Modify this according to your Docker repository
  }

  stages {
    stage('Build') {
      steps {
        // Build Docker image and tag it
        sh 'docker build -t healthcare-app .'
        sh 'docker tag healthcare-app $DOCKER_IMAGE'
      }
    }
    stage('Deploy') {
      steps {
        // Login to Docker registry
        withCredentials([usernamePassword(credentialsId: "${DOCKER_REGISTRY}", passwordVariable: 'DOCKER_PASSWORD', usernameVariable: 'DOCKER_USERNAME')]) {
          sh "echo \$DOCKER_PASSWORD | docker login -u \$DOCKER_USERNAME --password-stdin docker.io"
          // Push Docker image to registry
          sh 'docker push $DOCKER_IMAGE'
        }
      }
    }
  }

  post {
    always {
      // Logout from Docker registry
      sh 'docker logout'
    }
  }
}
