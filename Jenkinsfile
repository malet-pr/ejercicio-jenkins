pipeline {
    agent any
    
    parameters {
        choice(name: 'BRANCH', choices: ['desarrollo', 'produccion'], description: 'Seleccione la rama para desplegar')
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/malet-pr/ejercicio-jenkins.git'
            }
        }
        
    }
}
