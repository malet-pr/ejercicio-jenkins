pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials')
    }

    stages {
        stage('Initialization') {
            steps {
                script {
                    parameters {
                        choice(name: 'BRANCH', choices: ['desarrollo', 'produccion'], description: 'Seleccione la rama para desplegar')
                    }
                }
            }
        }

        stage('Checkout') {
            steps {
                script {
                    checkout([$class: 'GitSCM', branches: [[name: params.BRANCH]], 
                            doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
                            userRemoteConfigs: [[url: 'https://github.com/malet-pr/ejercicio-jenkins.git']],
                            credentialsId: GITHUB_CREDENTIALS])
                }
            }
        }
    }
}