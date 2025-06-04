pipeline {
    agent any

    environment {
        IMAGE = 'jaylyn1228/flask-ci-cd' // Docker Hub image name
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'git@github.com:JaylynF1228/Project-1.git', credentialsId: 'github-ssh'
            }
        }

        stage('Build Docker Image') {
            steps {
                sh 'docker build -t jaylyn1228/flask-ci-cd .'
            }
        }

        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh 'docker push jaylyn1228/flask-ci-cd'
                }
            }
        }

        stage('Deploy to EC2') {
            steps {
                sshagent(['ubuntu']) {
                    sh '''
                        ssh -o StrictHostKeyChecking=no ubuntu@35.175.150.192 '
                          docker pull jaylyn1228/flask-ci-cd &&
                          docker stop flask || true &&
                          docker rm flask || true &&
                          docker run -d --name flask -p 80:5000 jaylyn1228/flask-ci-cd
                        '
                    '''
                }
            }
        }
    }
}
