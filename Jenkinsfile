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
                
                // Génération du graphique de tendance (Trend Graph)
                dependencyCheckPublisher pattern: '**/dependency-check-report.xml'

                // Publication de la page HTML
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