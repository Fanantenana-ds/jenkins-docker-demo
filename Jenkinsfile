pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {
        IMAGE_NAME = "manaosoa/jenkins-docker-demo"
        CONTAINER_NAME = "jenkins-demo"
        DOCKER_CREDENTIALS = "dockerhub"
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Maven') {
            steps {
                bat 'mvn clean package'
            }
        }

        stage('Tests') {
            steps {
                bat 'mvn test'
            }
        }

        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    bat '''
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''
                }
            }
        }

        stage('Push Docker Hub') {
            steps {
                bat 'docker push %IMAGE_NAME%:latest'
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

        stage('Run New Container') {
            steps {
                bat '''
                docker run -d --name %CONTAINER_NAME% -p 8081:8080 %IMAGE_NAME%:latest
                '''
            }
        }
    }

    post {
        success {
            echo 'Pipeline exécutée avec succès !'
        }

        failure {
            echo 'Échec de la pipeline.'
        }
    }
}