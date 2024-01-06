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
                        sh 'apt install python3-Flask'
                        sh 'apt install python3-Redis'
                        sh 'apt install python3-gunicorn'
                        sh 'python3 app.py'
                    }
                }
            }
        }
    }
}


