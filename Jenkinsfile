pipeline {
    agent any

    environment {
        GITHUB_CREDENTIALS = credentials('github-credentials')
        DOCKERHUB_CREDENTIALS = credentials('dockerhub-credentials')
        DOCKERHUB_USERNAME = "${DOCKERHUB_CREDENTIALS_USR}"
        DOCKERHUB_PASSWORD = "${DOCKERHUB_CREDENTIALS_PSW}"
        DOCKER_IMAGE_NAME_VOTE = "${DOCKERHUB_USERNAME}/vote:latest"
        DOCKER_CONTAINER_NAME_VOTE = 'vote'
        DOCKER_IMAGE_NAME_RESULT = "${DOCKERHUB_USERNAME}/result:latest"
        DOCKER_CONTAINER_NAME_RESULT = 'result'
        DOCKER_IMAGE_NAME_WORKER = "${DOCKERHUB_USERNAME}/worker:latest"
        DOCKER_CONTAINER_NAME_WORKER = 'worker'        
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
                    docker.image(DOCKER_IMAGE_NAME_VOTE).run("-p 5000:80 --name $DOCKER_CONTAINER_NAME_VOTE -d")
                }
            }
        }
/*         stage('Run Tests') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_VOTE).inside("--workdir /app") {
                        sh 'python -m unittest discover'
                    }
                }
            }
        } */
        stage('Borra el contenedor de vote-app'){
            steps{
                script{
                    sh "docker stop $DOCKER_CONTAINER_NAME_VOTE"
                    sh "docker rm $DOCKER_CONTAINER_NAME_VOTE"
                }
            }
        }
        stage('Construir imagen de result-app') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_RESULT, "./result")
                }
            }
        }
        stage('Arrancar el contenedor de result-app') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_RESULT).run("-p 5001:80 --name $DOCKER_CONTAINER_NAME_RESULT -d")
                }
            }
        }
/*         stage('Run Tests') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_RESULT).inside("--workdir /app") {
                        //sh 'python -m unittest discover'
                    }
                }
            }
        } */
        stage('Borra el contenedor de result-app'){
            steps{
                script{
                    sh "docker stop $DOCKER_CONTAINER_NAME_RESULT"
                    sh "docker rm $DOCKER_CONTAINER_NAME_RESULT"
                }
            }
        }
    }


/*     post {
         ESTA PARTE VA SI PASAN LOS TESTS DE TODAS LAS APPS
        success {
            script {
                docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                    docker.image(DOCKER_IMAGE_NAME_VOTE).push()
                }
                echo 'Todos los tests pasaron, se publicó la imagen en DockerHub.'
            }
        }
        
        always {
            script {
                sh 'docker stop $DOCKER_CONTAINER_NAME_VOTE'
                sh 'docker rm $DOCKER_CONTAINER_NAME_VOTE'
            }
        }
    }*/
}


