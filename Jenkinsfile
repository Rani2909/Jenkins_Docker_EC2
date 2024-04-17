pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rani2909/myjenkin:latest' // Specify the Docker image tag here
        EC2_INSTANCE = 'ec2-44-201-170-191.compute-1.amazonaws.com'
        EC2_USER = 'ubuntu'
        SSH_CREDENTIALS = '7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2' // Updated to your SSH credentials ID
    }

    stages {
        stage('Checkout') {
            steps {
                // Checkout the source code from the GitHub repository
                git branch: 'main', url: 'https://github.com/Rani2909/Jenkins_Docker_EC2.git'
            }
        }
        
        stage('Build') {
            steps {
                // Build Docker image and tag it
                script {
                    docker.build('rani2909/myjenkin', '.')
                }
            }
        }
        
        stage('Deploy') {
            steps {
                // Cleanup existing containers
                script {
                    sshagent(credentials: [SSH_CREDENTIALS]) {
                        ssh "ubuntu@${EC2_INSTANCE} \"docker stop \$(docker ps -q --filter name=myjenkin) || true\""
                        ssh "ubuntu@${EC2_INSTANCE} \"docker rm \$(docker ps -aq --filter name=myjenkin) || true\""
                    }
                }
                
                // Deploy Docker image to EC2 instance
                script {
                    sshagent(credentials: [SSH_CREDENTIALS]) {
                        // SSH into EC2 instance and pull the Docker image
                        ssh "ubuntu@${EC2_INSTANCE} \"docker pull ${DOCKER_IMAGE}\""
                        ssh "ubuntu@${EC2_INSTANCE} \"docker run -d -p 8081:80 --name myjenkin ${DOCKER_IMAGE}\""
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
