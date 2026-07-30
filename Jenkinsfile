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

        // =================================================================
        // 1. DIAGNOSTIC DE L'ENVIRONNEMENT DOCKER
        // =================================================================
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

        // =================================================================
        // 2. CHECKOUT DU CODE
        // =================================================================
        stage('Checkout') {
            steps {
                git(
                    url: 'https://github.com/Fanantenana-ds/jenkins-docker-demo.git',
                    branch: 'master',
                    credentialsId: 'github-token'
                )
            }
        }

        // =================================================================
        // 3. BUILD MAVEN
        // =================================================================
        stage('Build Maven') {
            steps {
                bat 'mvn clean package'
            }
        }

        // =================================================================
        // 4. TESTS
        // =================================================================
        stage('Tests') {
            steps {
                bat 'mvn test'
            }
        }

        // =================================================================
        // 5. BUILD DOCKER IMAGE
        // =================================================================
        stage('Build Docker Image') {
            steps {
                bat 'docker build -t %IMAGE_NAME%:latest .'
            }
        }

        // =================================================================
        // 6. VÉRIFICATION DU CREDENTIAL DOCKER HUB
        // =================================================================
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
                            echo "   Assure-toi d'avoir généré un Personal Access Token sur Docker Hub."
                        }
                        echo "========================================"
                    }
                }
            }
        }

        // =================================================================
        // 7. DOCKER LOGIN (avec suppression du CRLF)
        // =================================================================
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

                        // Écrire le token dans un fichier sans CRLF
                        bat """
                            (echo|set /p="%DOCKER_PASS%") > token.txt
                            echo "Contenu du fichier token.txt :"
                            type token.txt
                            echo "---- FIN DU FICHIER ----"
                        """

                        // Exécuter le login avec le fichier
                        bat """
                            docker login -u %DOCKER_USER% --password-stdin < token.txt
                        """

                        // Nettoyer
                        bat "del token.txt"
                        echo "========================================"
                    }
                }
            }
        }

        // =================================================================
        // 8. PUSH DOCKER IMAGE
        // =================================================================
        stage('Push Docker Image') {
            steps {
                bat 'docker push %IMAGE_NAME%:latest'
            }
        }

        // =================================================================
        // 9. ARRÊT DE L'ANCIEN CONTENEUR
        // =================================================================
        stage('Stop Old Container') {
            steps {
                bat '''
                docker stop %CONTAINER_NAME% || exit /b 0
                docker rm %CONTAINER_NAME% || exit /b 0
                '''
            }
        }

        // =================================================================
        // 10. LANCEMENT DU NOUVEAU CONTENEUR
        // =================================================================
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

    // =================================================================
    // POST ACTIONS
    // =================================================================
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
            deleteDir()   // nettoie le workspace (pas besoin de plugin Workspace Cleanup)
            echo 'Fin de la pipeline.'
        }
    }
}