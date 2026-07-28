pipeline {
    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {
        IMAGE_NAME = "jenkins-docker-demo"
        CONTAINER_NAME = "jenkins-demo"
    }

    stages {

        stage('Clone') {
            steps {
                echo 'Le projet est déjà cloné par Jenkins.'
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
            echo 'Pipeline terminé avec succès !'
        }

        failure {
            echo 'Le pipeline a échoué.'
        }
    }
}