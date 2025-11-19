pipeline {
    agent any
    
    stages {
        stage('Récupération du code') {
            steps {
                echo "=== STAGE 1: Récupération du code ==="
                checkout scm
                echo "✓ Code récupéré avec succès"
            }
        }
        
        stage('Vérification Vagrant') {
            steps {
                echo "=== Vérification de l'environnement Vagrant ==="
                script {
                    if (!fileExists('Vagrantfile')) {
                        error "Vagrantfile non trouvé!"
                    }
                    // Vérifier si Vagrant est installé
                    sh 'vagrant --version'
                }
                echo "✓ Environnement Vagrant vérifié"
            }
        }
        
        stage('Démarrage VM et Tests') {
            steps {
                echo "=== STAGE 2: Exécution des tests dans Vagrant ==="
                sh '''
                    vagrant up
                    vagrant ssh -c "cd /vagrant && echo 'Installation des dépendances...' && npm install && echo 'Exécution des tests...' && npm test"
                '''
                echo "✓ Tests exécutés avec succès"
            }
        }
        
        stage('Build et Package') {
            steps {
                echo "=== STAGE 3: Création du bundle ==="
                sh '''
                    vagrant ssh -c "cd /vagrant && echo 'Construction du projet...' && npm run build"
                '''
                // Récupérer les artefacts depuis la VM
                sh 'vagrant ssh -c "cd /vagrant && tar -czf /tmp/bundle.tar.gz dist/"'
                sh 'vagrant ssh -c "cd /vagrant && cp /tmp/bundle.tar.gz ."'
                
                archiveArtifacts artifacts: 'bundle.tar.gz', fingerprint: true
                echo "✓ Bundle créé avec succès"
            }
        }
    }
    
    post {
        always {
            echo "Nettoyage de l'environnement"
            sh 'vagrant halt'  // Arrêter la VM après le build
            cleanWs()
        }
    }
}
