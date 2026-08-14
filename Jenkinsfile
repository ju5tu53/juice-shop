pipeline {
    agent any

    tools {
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
                bat 'npm install --ignore-scripts'
            }
        }

        stage('Dependency-Check Analysis') {
            steps {
                echo 'Analyse de sécurité en cours...'
                withCredentials([string(credentialsId: 'NVD_API_KEY', variable: 'NVD_API_KEY')]) {
                    dependencyCheck odcInstallation: 'DP-Check',
                                     additionalArguments: '--scan ./ --format ALL --out .'
                }
            }
        }

        stage('Publish Dependency-Check Report') {
            steps {
                echo 'Publication du rapport et du graphique...'

                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'

                publishHTML(target: [
                    allowMissing: false,
                    alwaysLinkToLastBuild: true,
                    keepAll: true,
                    reportDir: '.',
                    reportFiles: 'dependency-check-report.html',
                    reportName: 'Rapport OWASP Dependency-Check'
                ])
            }
        }
    }

    post {
        always {
            archiveArtifacts artifacts: '**/dependency-check-report.*, **/*.log', allowEmptyArchive: true
        }
        success {
            emailext (
                subject: "Pipeline réussi : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: """
                    Bonjour,
                    Le pipeline ${env.JOB_NAME} s'est terminé avec succès (build #${env.BUILD_NUMBER}).
                    Le rapport OWASP Dependency-Check est joint à cet e-mail.
                    Lien Jenkins : ${env.BUILD_URL}
                """,
                to: 'simacndione@gmail.com',
                attachmentsPattern:  'dependency-check-report.xml'
            )
        }
        failure {
            emailext (
                subject: "Échec du pipeline : ${env.JOB_NAME} #${env.BUILD_NUMBER}",
                body: "Le pipeline a échoué. Consulter les logs : ${env.BUILD_URL}console",
                to: 'simacndione@gmail.com'
            )
        }
    }
}