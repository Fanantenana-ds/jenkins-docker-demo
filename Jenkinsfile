pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {
        IMAGE_NAME = "jenkins-docker-demo"
        DOCKER_REPO = "manaosoa/jenkins-docker-demo"
        CONTAINER_NAME = "jenkins-demo"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME% .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: 'dockerhub',
                    usernameVariable: 'DOCKER_USER',
                    passwordVariable: 'DOCKER_PASS'
                )]) {
                    bat 'docker login -u %DOCKER_USER% -p %DOCKER_PASS%'
                }
            }
        }

        stage('Tag Docker Image') {
            steps {
                bat 'docker tag %IMAGE_NAME% %DOCKER_REPO%:latest'
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %DOCKER_REPO%:latest'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
docker stop %CONTAINER_NAME% || exit 0
docker rm %CONTAINER_NAME% || exit 0
'''
            }
        }

        stage('Run Docker Container') {
            steps {
                bat 'docker run -d -p 8081:8080 --name %CONTAINER_NAME% %IMAGE_NAME%'
            }
        }
    }

    post {
        success {
            echo 'Pipeline exécutée avec succès !'
        }

        failure {
            echo 'La pipeline a échoué.'
        }
    }
}