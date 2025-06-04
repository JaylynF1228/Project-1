pipeline {
    agent any

    environment {
        IMAGE = 'jaylynstrickland/flask-ci-cd' // Change to your Docker Hub username
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:JaylynF1228/Project-1.git', credentialsId: 'github-ssh'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t $IMAGE .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push $IMAGE'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ec2-ssh-key']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@35.175.150.192 '
                          docker pull $IMAGE &&
                          docker stop flask || true &&
                          docker rm flask || true &&
                          docker run -d --name flask -p 80:5000 $IMAGE
                        '
                    '''
                }
            }
        }
    }
}
