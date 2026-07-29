pipeline {

    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {

        IMAGE_NAME = "jenkins-docker-demo"
        DOCKERHUB_REPO = "manaosoa/jenkins-docker-demo"

        CONTAINER_NAME = "jenkins-demo"

        HOST_PORT = "8081"
        CONTAINER_PORT = "8080"
    }

    stages {

        stage('Informations') {
            steps {
                echo "======================================="
                echo "Début du pipeline Jenkins"
                echo "Projet : ${env.JOB_NAME}"
                echo "Build  : ${env.BUILD_NUMBER}"
                echo "======================================="
            }
        }

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
                bat '''
docker build -t %IMAGE_NAME% .
'''
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

        stage('Tag Docker Image') {

            steps {

                bat '''
docker tag %IMAGE_NAME% %DOCKERHUB_REPO%:latest
'''

            }

        }

        stage('Push Docker Hub') {

            steps {

                bat '''
docker push %DOCKERHUB_REPO%:latest
'''

            }

        }

        stage('Stop Ancien Conteneur') {

            steps {

                bat '''
docker stop %CONTAINER_NAME% || exit 0
docker rm %CONTAINER_NAME% || exit 0
'''

            }

        }

        stage('Lancer Nouveau Conteneur') {

            steps {

                bat '''
docker run -d ^
--name %CONTAINER_NAME% ^
-p %HOST_PORT%:%CONTAINER_PORT% ^
%IMAGE_NAME%
'''

            }

        }

    }

    post {

        success {

            echo ""
            echo "===================================="
            echo "Pipeline exécuté avec succès"
            echo "===================================="

            echo "Image Docker : ${DOCKERHUB_REPO}:latest"

            echo "Application : http://localhost:8081"

        }

        failure {

            echo ""
            echo "===================================="
            echo "Le pipeline a échoué"
            echo "Consultez les logs Jenkins"
            echo "===================================="

        }

        always {

            bat 'docker images'

        }

    }

}