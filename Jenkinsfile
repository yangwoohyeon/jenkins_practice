pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "yangwoohyeon/jenkins-practice"
        DEPLOY_HOST = "ubuntu@3.36.104.109"

    }

    stages {

        stage('Build') {
            steps {
                sh './gradlew clean build -x test'
            }
        }

        stage('Docker Build') {
            steps {
                sh "docker build -t $DOCKER_IMAGE ."
            }
        }

        stage('Push to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-credentials', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }

        stage('Deploy to EC2-2') {
            steps {
                sshagent(['ec2-key']) {
                   sh """
                                           ssh -o StrictHostKeyChecking=no $DEPLOY_HOST "docker pull $DOCKER_IMAGE"
                                           ssh -o StrictHostKeyChecking=no $DEPLOY_HOST "cd ~/deploy && docker-compose down"
                                           ssh -o StrictHostKeyChecking=no $DEPLOY_HOST "cd ~/deploy && docker-compose up -d"
                                       """
                }
            }
        }

    }
}
