pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rani2909/myjenkin' // Modify this according to your Docker repository
        EC2_INSTANCE = 'ec2-44-201-170-191.compute-1.amazonaws.com'
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
                // Cleanup existing containers
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        try {
                            ssh "ubuntu@${EC2_INSTANCE} \"docker stop \$(docker ps -q --filter ancestor=${DOCKER_IMAGE}) || true\""
                            ssh "ubuntu@${EC2_INSTANCE} \"docker rm \$(docker ps -aq --filter ancestor=${DOCKER_IMAGE}) || true\""
                        } catch (Exception e) {
                            echo "Error cleaning up containers: ${e}"
                        }
                    }
                }
                // Deploy Docker image to EC2 instance
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        try {
                            ssh "ubuntu@${EC2_INSTANCE} \"docker pull ${DOCKER_IMAGE}\""
                            ssh "ubuntu@${EC2_INSTANCE} \"docker run -d -p 8081:80 ${DOCKER_IMAGE}\""
                        } catch (Exception e) {
                            echo "Error deploying Docker image: ${e}"
                        }
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
