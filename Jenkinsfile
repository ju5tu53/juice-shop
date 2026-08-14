pipeline {
    agent any

    tools {
        // Nom configuré dans Administrer Jenkins > Tools
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
                // Utilisation de bat pour Windows
                bat 'npm install --ignore-scripts'
            }
        }

        stage('Dependency-Check Analysis') {
            steps {
                echo 'Analyse de sécurité en cours...'
                // Exécution de l'analyse avec l'outil 'DP-Check' (génère XML et HTML via --format ALL)
                dependencyCheck installation: 'DP-Check', arguments: '--scan ./ --format ALL --out .'
            }
        }

        stage('Publish Dependency-Check Report') {
            steps {
                echo 'Publication du rapport et du graphique de tendance...'
                
                // 1. Publication du XML pour générer le graphique de tendance (Trend Graph)
                dependencyCheckPublisher(pattern: '**/dependency-check-report.xml')

                // 2. Publication du HTML pour afficher le menu "Rapport OWASP Dependency-Check"
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
            // Conservation des rapports comme artefacts téléchargeables
            archiveArtifacts artifacts: '**/dependency-check-report.*, **/*.log', allowEmptyArchive: true
        }
        failure {
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
