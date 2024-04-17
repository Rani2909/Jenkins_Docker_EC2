pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = 'rani2909/mywebsite:latest'
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
                    // Docker Hub credentials
                    withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                        sh 'docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD'
                        sh "docker build . --file Dockerfile --tag $DOCKER_IMAGE"
                    }
                }
            }
        }
        
        stage('Deploy') {
            steps {
                script {
                    // SSH credentials
                    withCredentials([sshUserPrivateKey(credentialsId: '7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2', keyFileVariable: 'SSH_KEY_FILE', passphraseVariable: '', usernameVariable: 'SSH_USERNAME')]) {
                        sshagent(credentials: ['7a8d0b4d-ff87-40a6-93c6-7844d2c7d3f2']) {
                            sh "ssh -i $SSH_KEY_FILE ${EC2_USER}@${EC2_INSTANCE} \"docker stop mywebsite && docker rm mywebsite || true\""
                            sh "ssh -i $SSH_KEY_FILE ${EC2_USER}@${EC2_INSTANCE} \"docker pull $DOCKER_IMAGE\""
                            sh "ssh -i $SSH_KEY_FILE ${EC2_USER}@${EC2_INSTANCE} \"docker run -d -p 8082:80 --name mywebsite $DOCKER_IMAGE\""
                        }
                    }
                }
            }
        }
    }
    
    post {
        always {
            script {
                // Docker Hub logout
                sh 'docker logout'
            }
        }
    }
}
