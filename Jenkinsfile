pipeline {
    agent any
    
    tools {
        maven 'maven'
    }

    stages {
        stage('Git Clone') {
            steps {
                script {
                    echo 'Clonage du dépôt Git...'
                    checkout([$class: 'GitSCM',
                        branches: [[name: '*/main']],
                        userRemoteConfigs: [[url: 'https://github.com/imadev26/jenkins-cicd-demo.git']]
                    ])
                }
            }
        }

        stage('Build') {
            steps {
                echo 'Compilation du projet avec Maven...'
                bat 'mvn clean install'
            }
        }

        stage('Create Docker Image') {
            steps {
                echo 'Création de l\'image Docker...'
                bat 'docker build -t jenkins-demo:latest .'
            }
        }

        stage('Run') {
            steps {
                script {
                    echo 'Arrêt et suppression du conteneur existant (si présent)...'
                    bat '''
                        docker stop jenkins-demo-container || exit 0
                        docker rm jenkins-demo-container || exit 0
                    '''
                    
                    echo 'Lancement du nouveau conteneur...'
                    bat 'docker run --name jenkins-demo-container -d -p 8585:8282 jenkins-demo:latest'
                    
                    echo 'Application déployée sur http://localhost:8585'
                }
            }
        }
    }
    
    post {
        success {
            echo '✅ Pipeline exécuté avec succès!'
            echo 'Testez l\'application: http://localhost:8585/api/hello'
        }
        failure {
            echo '❌ Le pipeline a échoué. Consultez les logs ci-dessus.'
        }
    }
}
