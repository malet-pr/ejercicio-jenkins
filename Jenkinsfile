pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials')
    }

    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM', 
                            branches: [
                                [name: 'desarrollo'],
                                [name: 'produccion']
                            ], 
                            doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
                            userRemoteConfigs: [[url: 'https://github.com/malet-pr/ejercicio-jenkins.git']],
                            credentialsId: 'github-credentials'])
                }
            }
        }
        stage('Construir "vote" app') {
            steps {
                script {
                    dir('vote') {
                        sh 'pip install --no-cache-dir -r requirements.txt'
                        sh 'python3 app.py'
                    }
                }
            }
        }
    }
}