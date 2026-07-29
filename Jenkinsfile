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

   stage('Docker Login') {
    steps {
        bat 'docker login -u manaosoa -p TON_DOCKER_TOKEN'
    }
}

    post {

        success {
            echo "Pipeline terminée avec succès."
        }

        failure {
            echo "La pipeline a échoué."
        }

        always {
            cleanWs()
        }
    }
}