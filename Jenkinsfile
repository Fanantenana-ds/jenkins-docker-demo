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

        stage('Diagnostic Docker') {
            steps {
                bat '''
                echo ========================================
                echo DIAGNOSTIC DOCKER
                echo ========================================
                where docker
                docker --version
                docker context show
                whoami
                '''
            }
        }

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

        stage('Check Docker Credential') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'jeton Docker Hub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    script {

                        def len = env.DOCKER_PASS.length()

                        echo "========================================"
                        echo "Vérification du credential Docker Hub"
                        echo "========================================"
                        echo "Utilisateur : ${env.DOCKER_USER}"
                        echo "Longueur PAT : ${len}"

                        if (env.DOCKER_PASS.startsWith("dckr_pat")) {
                            echo "Format PAT : OK"
                        } else {
                            echo "Format PAT : INCORRECT"
                        }

                        echo "========================================"

                    }
                }
            }
        }

        stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'jeton Docker Hub',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {

                    bat '''
                    @echo off
                    echo Connexion Docker Hub...
                    echo %DOCKER_PASS%| docker login -u %DOCKER_USER% --password-stdin
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
                docker stop %CONTAINER_NAME% >nul 2>&1
                docker rm %CONTAINER_NAME% >nul 2>&1
                exit /b 0
                '''
            }
        }

        stage('Run Docker Container') {
            steps {
                bat '''
                docker run -d --name %CONTAINER_NAME% -p 8081:8080 %IMAGE_NAME%:latest
                '''
            }
        }

    }

    post {

        success {
            echo "========================================"
            echo "Pipeline exécutée avec succès."
            echo "========================================"
        }

        failure {
            echo "========================================"
            echo "La pipeline a échoué."
            echo "========================================"
        }

        always {
            deleteDir()
            echo "Fin de la pipeline."
        }

    }

}