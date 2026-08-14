pipeline {
    agent any

    tools {
        nodejs 'NodeJS-20'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
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
                bat 'npm install'
            }
        }

        stage('Dependency-Check Analysis') {
            steps {
                dependencyCheck additionalArguments: '--format HTML --format XML --nvdApiKey %NVD_API_KEY%', odcInstallation: 'DP-Check'
            }
        }

        stage('Publish Dependency-Check Report') {
            steps {
                dependencyCheckPublisher pattern: 'dependency-check-report.xml'
                publishHTML(target: [
                    reportDir: '.',
                    reportFiles: 'dependency-check-report.html',
                    reportName: 'Rapport OWASP Dependency-Check'
                ])
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: 'dependency-check-report.*', fingerprint: true
        }
        success {
            emailext (
                subject: "Pipeline réussi : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le pipeline s'est terminé avec succès. Voir le rapport joint.",
                to: 'destinataire@example.com',
                attachmentsPattern: 'dependency-check-report.html'
            )
        }
        failure {
            emailext (
                subject: "Échec du pipeline : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le pipeline a échoué. Consulter les logs Jenkins pour plus de détails.",
                to: 'destinataire@example.com'
            )
        }
    }
}