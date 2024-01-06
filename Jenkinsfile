pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials')
        DOCKERHUB_USERNAME = credentials('dockerhub-credentials-id').username
        DOCKERHUB_PASSWORD = credentials('dockerhub-credentials-id').password
        DOCKER_IMAGE_NAME_VOTE = "${DOCKERHUB_USERNAME}/vote:latest"
        DOCKER_CONTAINER_NAME_VOTE = 'vote'
    }

    stages {
        stage('Checkout repositorio') {
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
        stage('Construir imagen de vote-app') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_VOTE, "./vote")
                }
            }
        }
        stage('Arrancar el contenedor de vote-app') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_VOTE).run("-p 5000:80", "--name $DOCKER_CONTAINER_NAME_VOTE -d")
                }
            }
        }
    }
}


