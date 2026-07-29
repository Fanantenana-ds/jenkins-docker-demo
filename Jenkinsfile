pipeline {

    agent any


    tools {
        maven 'Maven'
        jdk 'JDK17'
    }


    environment {
        IMAGE_NAME = "manaosoa/jenkins-docker-demo"
    }


    stages {


        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/Fanantenana-ds/jenkins-docker-demo.git',
                    branch: 'master',
                    credentialsId: 'github-token'
                )
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
                        credentialsId: 'dockerhub-token',
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
                docker stop jenkins-demo || exit 0
                docker rm jenkins-demo || exit 0
                '''
            }
        }



        stage('Run New Container') {
            steps {
                bat '''
                docker run -d ^
                --name jenkins-demo ^
                -p 8081:8080 ^
                %IMAGE_NAME%:latest
                '''
            }
        }

    }



    post {

        success {
            echo 'Pipeline réussie'
        }

        failure {
            echo 'Échec de la pipeline'
        }

    }

}