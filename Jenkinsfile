pipeline {
    agent any

    tools {
        // Assurez-vous que le nom 'NodeJS-20' correspond exactement
        // à celui configuré dans Administrer Jenkins > Tools
        nodejs 'NodeJS-20'
    }

    stages {
        stage('Checkout') {
            steps {
                echo 'Récupération du code depuis GitHub...'
                checkout scm
            }
        }

        stage('Install dependencies') {
            steps {
                // Utilisation de --ignore-scripts pour éviter les erreurs liées à Python/node-gyp
                bat 'npm install --ignore-scripts'
            }
        }

        stage('Dependency-Check Analysis') {
            steps {
                echo 'Analyse de sécurité en cours...'
                // Ajoutez ici la commande de scan si vous utilisez Dependency-Check CLI
                // bat 'dependency-check --project "Juice Shop" --scan .'
            }
        }

        stage('Publish Dependency-Check Report') {
            steps {
                echo 'Publication du rapport de sécurité...'
                // dependencyCheckPublisher(pattern: '**/dependency-check-report.xml')
            }
        }
    }

    post {
        always {
            // Archivage des artefacts si nécessaire
            archiveArtifacts artifacts: '**/*.log', allowEmptyArchive: true
        }
        failure {
            // Isolation de l'envoi de mail pour que le job ne plante pas sur une erreur SMTP
            catchError(buildResult: 'FAILURE', stageResult: 'FAILURE') {
                emailext (
                    subject: "Échec du build Jenkins : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                    body: "Le build a échoué. Vérifiez les logs sur Jenkins : ${env.BUILD_URL}",
                    to: 'destinataire@example.com'
                )
            }
        }
    }
}