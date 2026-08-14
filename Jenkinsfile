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
                dependencyCheck installation: 'DP-Check', arguments: '--scan ./ --format ALL --out .'
            }
        }

        stage('Publish Dependency-Check Report') {
            steps {
                echo 'Publication du rapport et du graphique...'
                
                // Génération du graphique de tendance
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'

                // Publication du rapport HTML dans le menu latéral
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
    }
}