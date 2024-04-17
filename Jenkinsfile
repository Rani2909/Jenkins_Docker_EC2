pipeline {
    agent any

    environment {
        DOCKER_IMAGE = 'rani2909/myjenkin:latest'
        EC2_INSTANCE = 'ec2-44-206-241-86.compute-1.amazonaws.com'
        EC2_USER = 'ubuntu'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Rani2909/Jenkins_Docker_EC2.git'
            }
        }
        
        stage('Build') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', 'dockerhub') {
                        def customImage = docker.build(DOCKER_IMAGE, "-f Dockerfile .")
                        customImage.push()
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                        sshCommand remote: "ubuntu@${EC2_INSTANCE}", command: """
                            docker stop myjenkin || true
                            docker rm myjenkin || true
                            docker pull $DOCKER_IMAGE
                            docker run -d -p 8082:80 --name myjenkin $DOCKER_IMAGE
                        """
                    }
                }
            }
        }
    }

    post {
        always {
            script {
                docker.image(DOCKER_IMAGE).push('latest')
            }
            script {
                sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                    sshCommand remote: "ubuntu@${EC2_INSTANCE}", command: "docker ps"
                }
            }
        }
    }
}
