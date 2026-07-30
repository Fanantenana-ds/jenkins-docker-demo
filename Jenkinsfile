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
                echo "========================================"
                echo "   DIAGNOSTIC DOCKER"
                echo "========================================"
                echo "1. Où se trouve l'exécutable docker ?"
                where docker
                echo ""
                echo "2. Version de Docker :"
                docker --version
                echo ""
                echo "3. Contextes Docker disponibles :"
                docker context ls
                echo ""
                echo "4. Contexte actif :"
                docker context show
                echo ""
                echo "5. Utilisateur actuel :"
                whoami
                echo ""
                echo "6. USERPROFILE : %USERPROFILE%"
                echo "========================================"
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
                        credentialsId: 'dockerhub-token',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    script {
                        def passLen = env.DOCKER_PASS.length()
                        def firstChars = env.DOCKER_PASS.substring(0, Math.min(4, passLen))
                        def lastChars = env.DOCKER_PASS.substring(Math.max(0, passLen - 4))
                        echo "========================================"
                        echo "   VÉRIFICATION DU CREDENTIAL"
                        echo "========================================"
                        echo "Utilisateur Docker : ${env.DOCKER_USER}"
                        echo "Longueur du token  : ${passLen} caractères"
                        echo "Début du token     : ${firstChars}..."
                        echo "Fin du token       : ...${lastChars}"
                        if (env.DOCKER_PASS.startsWith("dckr_pat")) {
                            echo "✅ Le token commence par 'dckr_pat' (format PAT valide)."
                        } else {
                            echo "⚠️  ATTENTION : le token ne commence pas par 'dckr_pat'."
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
                        credentialsId: 'dockerhub-token',
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    script {
                        echo "========================================"
                        echo "   TENTATIVE DE LOGIN DOCKER HUB"
                        echo "========================================"
                        echo "Token complet (affiché pour diagnostic) :"
                        echo "${env.DOCKER_PASS}"
                        echo "----------------------------------------"

                        bat """
                            (echo|set /p="%DOCKER_PASS%") > token.txt
                            echo "Contenu du fichier token.txt :"
                            type token.txt
                            echo "---- FIN DU FICHIER ----"
                        """

                        bat """
                            docker login -u %DOCKER_USER% --password-stdin < token.txt
                        """

                        bat "del token.txt"
                        echo "========================================"
                    }
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
            echo '========================================'
            echo '✅ PIPELINE EXÉCUTÉE AVEC SUCCÈS'
            echo '========================================'
        }
        failure {
            echo '========================================'
            echo '❌ LA PIPELINE A ÉCHOUÉ'
            echo '========================================'
        }
        always {
            deleteDir()
            echo 'Fin de la pipeline.'
        }
    }
}