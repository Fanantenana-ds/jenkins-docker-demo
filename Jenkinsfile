pipeline {

    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.16'
    }

    environment {
        IMAGE_NAME = "manaosoa/jenkins-docker-demo"
        CONTAINER_NAME = "jenkins-demo"
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
                    echo ================================
                    echo Docker User : %DOCKER_USER%
                    echo Login Docker Hub...
                    echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin
                    '''

                }
            }
        }

        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%:latest'
            }
        }

        stage('Stop Old Container') {
            steps {
                bat '''
                docker stop %CONTAINER_NAME% || exit /b 0
                docker rm %CONTAINER_NAME% || exit /b 0
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                docker run -d ^
                --name %CONTAINER_NAME% ^
                -p 8081:8080 ^
                %IMAGE_NAME%:latest
                '''
            }
        }

    }

    post {

        success {
            echo '====================================='
            echo 'Pipeline exécutée avec succès !'
            echo '====================================='
        }

        failure {
            echo '====================================='
            echo 'La pipeline a échoué.'
            echo '====================================='
        }

        always {
            echo 'Fin de la pipeline.'
        }

    }

}