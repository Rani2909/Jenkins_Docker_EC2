pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rani2909/myjenkin' // Modify this according to your Docker repository
        EC2_INSTANCE = 'ec2-44-201-170-191.compute-1.amazonaws.com'
        EC2_USER = 'ubuntu' // or the user you use to SSH into your EC2 instance
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
                sh 'docker build -t healthcare-app .'
                sh 'docker tag healthcare-app $DOCKER_IMAGE'
            }
        }
        
        stage('Deploy') {
            steps {
                // Cleanup existing containers
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        ssh "ubuntu@${EC2_INSTANCE} \"docker stop \$(docker ps -q --filter ancestor=${DOCKER_IMAGE}) || true\""
                        ssh "ubuntu@${EC2_INSTANCE} \"docker rm \$(docker ps -aq --filter ancestor=${DOCKER_IMAGE}) || true\""
                    }
                }
                
                // Deploy Docker image to EC2 instance
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        // SSH into EC2 instance and pull the Docker image
                        ssh "ubuntu@${EC2_INSTANCE} \"docker pull ${DOCKER_IMAGE}\""
                        ssh "ubuntu@${EC2_INSTANCE} \"docker run -d -p 8081:80 ${DOCKER_IMAGE}\""
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
