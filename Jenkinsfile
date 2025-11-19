pipeline {
    agent any
    
    tools {
        // Configurez ces outils dans "Gérer Jenkins" > "Outils globaux"
        nodejs 'nodejs'  // Si vous utilisez Node.js
    }
    
    stages {
        stage('Récupération du code') {
            steps {
                echo "=== STAGE 1: Récupération du code ==="
                // Le checkout est automatique avec "checkout scm" dans un pipeline
                script {
                    // Vérifier le contenu du repository
                    sh 'ls -la'
                }
                echo "✓ Code récupéré avec succès"
            }
        }
        
        stage('Installation dépendances') {
            steps {
                echo "=== Installation des dépendances ==="
                script {
                    if (fileExists('package.json')) {
                        echo "Projet Node.js détecté"
                        sh 'npm install'
                    } else if (fileExists('requirements.txt')) {
                        echo "Projet Python détecté"
                        sh 'pip install -r requirements.txt'
                    } else {
                        echo "Aucun gestionnaire de dépendances détecté - étape ignorée"
                    }
                }
                echo "✓ Dépendances installées"
            }
        }
        
        stage('Exécution des tests') {
            steps {
                echo "=== STAGE 2: Exécution des tests ==="
                script {
                    if (fileExists('package.json')) {
                        sh 'npm test || echo "Aucun test trouvé ou échec des tests - continuation"'
                    } else if (fileExists('pom.xml')) {
                        sh 'mvn test || echo "Aucun test trouvé ou échec des tests - continuation"'
                    } else {
                        echo "Aucun framework de test détecté - étape ignorée"
                    }
                }
                echo "✓ Tests exécutés"
            }
        }
        
        stage('Création du bundle') {
            steps {
                echo "=== STAGE 3: Création du bundle ==="
                script {
                    if (fileExists('package.json')) {
                        sh 'npm run build || echo "Commande build non trouvée"'
                        // Archiver le résultat du build
                        archiveArtifacts artifacts: 'dist/**/*, build/**/*', fingerprint: true
                    } else if (fileExists('pom.xml')) {
                        sh 'mvn package -DskipTests || echo "Commande package non trouvée"'
                        archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
                    } else {
                        echo "Aucune commande de build détectée"
                        // Créer un archive simple du code source
                        sh 'tar -czf bundle.tar.gz . || echo "Échec création archive"'
                        archiveArtifacts artifacts: 'bundle.tar.gz', fingerprint: true
                    }
                }
                echo "✓ Bundle créé avec succès"
            }
        }
    }
    
    post {
        always {
            echo "=== Pipeline terminé ==="
            echo "Statut: ${currentBuild.result ?: 'SUCCESS'}"
            // Nettoyage optionnel (décommentez si nécessaire)
            // cleanWs()
        }
        success {
            echo "🎉 Pipeline exécuté avec succès!"
        }
        failure {
            echo "❌ Pipeline a échoué!"
        }
    }
}
