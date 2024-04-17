pipeline {
  agent any

  environment {
    DOCKER_IMAGE = 'rani2909/myjenkin' // Modify this according to your Docker repository
    EC2_INSTANCE = 'ec2-3-87-72-11.compute-1.amazonaws.com'
    EC2_USER = 'ubuntu' // or the user you use to SSH into your EC2 instance
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
        // Deploy Docker image to EC2 instance
        script {
          sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
            // SSH into EC2 instance and pull the Docker image
            ssh "ssh ubuntu@ec2-3-87-72-11.compute-1.amazonaws.com "docker pull rani2909/myjenkin""
          }
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
