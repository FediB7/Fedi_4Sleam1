pipeline {
    agent any

    stages {
        stage('Récupération du code') {
            steps {
                echo "=== STAGE 1: Récupération du code ==="
                // Remplacer par ton URL et branche
                git branch: 'main', url: 'https://github.com/FediB7/Fedi_4Sleam1.git'
                sh 'ls -la'
                echo "✓ Code récupéré avec succès"
            }
        }

        stage('Création du livrable') {
            steps {
                echo "=== STAGE 2: Création du livrable ==="
                script {
                    if (fileExists('pom.xml')) {
                        sh 'chmod +x mvnw'
                        sh './mvnw package -DskipTests || echo "Échec de la création du livrable"'
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    } else {
                        sh 'tar -czf bundle.tar.gz . || echo "Échec création archive"'
                        archiveArtifacts artifacts: 'bundle.tar.gz', fingerprint: true
                    }
                }
                echo "✓ Livrable créé"
            }
        }
    }

    post {
        always {
            echo "=== Pipeline terminé ==="
            echo "Statut: ${currentBuild.result ?: 'SUCCESS'}"
        }
        success {
            echo "🎉 Pipeline exécuté avec succès !"
        }
        failure {
            echo "❌ Pipeline a échoué !"
        }
    }
}
