pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rani2909/mywebsite:latest' // Modify this according to your Docker repository
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
                // Login to Docker registry
                sh 'docker login -u <username> -p <password>'
                
                // Build Docker image and tag it
                sh 'docker build . --file Dockerfile --tag $DOCKER_IMAGE'
            }
        }
        
        stage('Deploy') {
            steps {
                // Cleanup existing containers
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        ssh "${EC2_USER}@${EC2_INSTANCE} \"docker stop mywebsite && docker rm mywebsite || true\""
                    }
                }
                
                // Deploy Docker image to EC2 instance
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        // SSH into EC2 instance and pull the Docker image
                        ssh "${EC2_USER}@${EC2_INSTANCE} \"docker pull $DOCKER_IMAGE\""
                        
                        // Run a new container
                        ssh "${EC2_USER}@${EC2_INSTANCE} \"docker run -d -p 80:80 --name mywebsite $DOCKER_IMAGE\""
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
