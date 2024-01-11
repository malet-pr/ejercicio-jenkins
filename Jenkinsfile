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
        stage('Obtener repositorio') {
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
        stage('tests para vote-app') {
            steps {
                script {
                    sh 'echo No hay tests de vote-app'
                }
            }
        } 
        stage('Construir imagen de vote-app y subirla a Docker Hub') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_VOTE, "./vote")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE_NAME_VOTE).push()
                    } 
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
/*         stage('Arrancar el contenedor de result-app') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_RESULT).run("-p 5001:80 --name $DOCKER_CONTAINER_NAME_RESULT -d")
                }
            }
        } */
/*         stage('tests para result-app') {
            steps {
                script {
                    docker.image(DOCKER_IMAGE_NAME_RESULT).inside("--workdir /app") {
                        //sh 'python -m unittest discover'
                        // comando para tests
                    }
                }
            }
        } */
        stage('Subir imagen result-app'){
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE_NAME_RESULT).push()                       
                    }
                    // sh 'docker stop $DOCKER_CONTAINER_NAME_RESULT'
                    // sh 'docker rm $DOCKER_CONTAINER_NAME_RESULT' 
                }
            }
        }

        stage('Construir imagen de worker-app') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_WORKER, "./worker")
                }
            }
        }
        stage('tests para worker-app') {
            steps {
                script {
                    sh 'echo No hay tests de worker-app'
                }
            }
        } 
        stage('Subir imagen worker-app'){
            steps {
                script {
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE_NAME_WORKER).push()
                    }                
                }
            }
        }

        stage('Deploy Redis') {
            steps {
                script {
                    sh 'kubectl apply -f ./kubernetes/redis.yaml -n voting-app'
                }
            }
        }
        stage('Deploy PostgreSQL') {
            steps {
                script {
                    sh 'kubectl apply -f ./kubernetes/postgres.yaml -n voting-app'
                }
            }
        }
        stage('Deploy Vote App') {
            steps {
                script {
                    sh 'kubectl apply -f ./kubernetes/vote-app.yaml -n voting-app'
                }
            }
        }
        stage('Deploy Result App') {
            steps {
                script {
                    sh 'kubectl apply -f ./kubernetes/result-app.yaml -n voting-app'
                }
            }
        }
        stage('Deploy Worker App') {
            steps {
                script {
                    sh 'kubectl apply -f ./kubernetes/worker-app.yaml -n voting-app'
                }
            }
        }
    }
}


