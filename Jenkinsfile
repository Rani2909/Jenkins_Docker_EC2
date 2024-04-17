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
                // Checkout source code from GitHub repository
                git 'https://github.com/Rani2909/Jenkins_Docker_EC2.git'
                
                // Build Docker image
                script {
                    docker.build("healthcare-app")
                }
            }
        }
        stage('Deploy') {
            steps {
                // SSH into EC2 instance and deploy Docker image
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        ssh "ubuntu@${EC2_INSTANCE} 'docker pull ${DOCKER_IMAGE}'"
                        ssh "ubuntu@${EC2_INSTANCE} 'docker stop healthcare-app || true'"
                        ssh "ubuntu@${EC2_INSTANCE} 'docker rm healthcare-app || true'"
                        ssh "ubuntu@${EC2_INSTANCE} 'docker run -d -p 8081:80 --name healthcare-app ${DOCKER_IMAGE}'"
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
