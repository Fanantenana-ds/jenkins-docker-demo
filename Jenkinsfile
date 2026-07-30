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

        // ------------------------------------------------------------
        // Étape de diagnostic : vérifier que le credential est récupéré
        // ------------------------------------------------------------
        stage('Check Docker Credential') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub-token',  // ← vérifie que cet ID est correct
                        usernameVariable: 'DOCKER_USER',
                        passwordVariable: 'DOCKER_PASS'
                    )
                ]) {
                    script {
                        // Récupérer la longueur du mot de passe
                        def passLen = env.DOCKER_PASS.length()
                        def firstChars = env.DOCKER_PASS.substring(0, Math.min(4, passLen))
                        def lastChars = env.DOCKER_PASS.substring(Math.max(0, passLen - 4))
                        echo "Utilisateur Docker : ${env.DOCKER_USER}"
                        echo "Longueur du token : ${passLen} caractères"
                        echo "Début du token    : ${firstChars}..."
                        echo "Fin du token      : ...${lastChars}"
                        // Vérifier que le token commence par "dckr_pat" (format PAT)
                        if (env.DOCKER_PASS.startsWith("dckr_pat")) {
                            echo "Le token semble avoir le bon format PAT."
                        } else {
                            echo "⚠️ Attention : le token ne commence pas par 'dckr_pat' !"
                            echo "   Il s'agit peut-être d'un mot de passe, pas d'un token."
                            echo "   Assure-toi d'avoir généré un Personal Access Token sur Docker Hub."
                        }
                    }
                }
            }
        }

        // ------------------------------------------------------------
        // Tentative de login avec logs détaillés
        // ------------------------------------------------------------
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
                        // On exécute la commande de login et on capture la sortie
                        def loginCmd = "echo %DOCKER_PASS% | docker login -u %DOCKER_USER% --password-stdin"
                        bat """
                            echo "========================================="
                            echo "Tentative de login Docker..."
                            echo "Utilisateur : %DOCKER_USER%"
                            echo "Commande : ${loginCmd}"
                            echo "========================================="
                            ${loginCmd}
                        """
                        // La commande echo %DOCKER_PASS% sera masquée par Jenkins (masking)
                        // mais on peut ajouter une redirection de sortie pour voir l'erreur
                        bat """
                            echo "Test avec 'docker login' sans stdin pour voir l'erreur :"
                            docker login -u %DOCKER_USER% -p %DOCKER_PASS% || echo "ERREUR : le login a échoué."
                            echo "========================================="
                        """
                        // Note : la deuxième commande est moins sécurisée mais donne une erreur explicite.
                    }
                }
            }
        }

        // ------------------------------------------------------------
        // Après la correction, on peut pousser l'image
        // ------------------------------------------------------------
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
            // Nettoie le workspace (remplace cleanWs() par deleteDir() pour éviter l'erreur)
            deleteDir()
            echo 'Fin de la pipeline.'
        }

    }
}