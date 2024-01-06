pipeline {
    agent any
    
    parameters {
        choice(name: 'BRANCH', choices: ['desarrollo', 'produccion'], description: 'Seleccione la rama para desplegar')
    }

    tools {
        git 'Default' 
    }
    
    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/malet-pr/ejercicio-jenkins.git'
            }
        }
        
        // agrego para ver si corre jenkins
    }
}
