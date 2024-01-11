def formatDate(long timestamp) {
    def sdf = new java.text.SimpleDateFormat("dd/MM/yyyy - HH:mm:ss - z", new java.util.Locale("es", "ES"))
    sdf.timeZone = TimeZone.getTimeZone("GMT-3")
    return sdf.format(new Date(timestamp))
}

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
        DOCKER_IMAGE_NAME_RESULT_TEST = "${DOCKERHUB_USERNAME}/result-test"
        DOCKER_CONTAINER_NAME_RESULT_TEST = 'result-test'
        DOCKER_IMAGE_NAME_WORKER = "${DOCKERHUB_USERNAME}/worker:latest"
        DOCKER_CONTAINER_NAME_WORKER = 'worker'   
        DISCORD_WEBHOOK_URL = "https://discord.com/api/webhooks/1195123870039019570/CF4atSic8tBkRhrvQyN-z66kG9MF2EPcPQzGlX8_KkOVPeSUy-qgys_Twh-da5hRDEE4"
    }
    
    stages {
        stage('Obtener repositorio') {
            steps {
                script {
                    checkout([
                        $class: 'GitSCM', 
                        branches: [
                            [name: 'desarrollo'],
                            [name: 'produccion']
                        ], 
                        doGenerateSubmoduleConfigurations: false, extensions: [], submoduleCfg: [], 
                        userRemoteConfigs: [[url: 'https://github.com/malet-pr/ejercicio-jenkins.git']],
                        credentialsId: 'github-credentials'
                    ])
                }
            }
        }
        stage('tests para vote-app') {
            steps {
                script {
                    sh 'echo "No hay tests de vote-app"'
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
        stage('tests para result-app') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_RESULT_TEST, "./result/tests") 
                    docker.image(DOCKER_IMAGE_NAME_RESULT_TEST).run("--name $DOCKER_CONTAINER_NAME_RESULT_TEST -d")
                    sh 'docker rm $DOCKER_CONTAINER_NAME_RESULT_TEST --force'
                    sh 'docker rmi $DOCKER_IMAGE_NAME_RESULT_TEST'
                }
            }
        }        
        stage('Construir imagen de result-app y subirla a Docker Hub') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_RESULT, "./result")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE_NAME_RESULT).push()
                    } 
                }
            }
        }
        stage('tests para worker-app') {
            steps {
                script {
                    sh 'echo "No hay tests de worker-app"'
                }
            }
        } 
        stage('Construir imagen de worker-app y subir a Docker Hub') {
            steps {
                script {
                    docker.build(DOCKER_IMAGE_NAME_WORKER, "./worker")
                    docker.withRegistry('https://registry.hub.docker.com', 'dockerhub-credentials') {
                        docker.image(DOCKER_IMAGE_NAME_WORKER).push()
                    } 
                }                     
            }
        }
        stage('Instalar envsubst') {
            steps {
                script {
                    sh 'apt-get update && apt-get install -y gettext-base'
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
                    sh "envsubst < ./kubernetes/vote-app.yaml | kubectl apply -n voting-app -f -"
                    //sh 'kubectl apply -f ./kubernetes/vote-app.yaml -n voting-app'
                }
            }
        }
        stage('Deploy Result App') {
            steps {
                script {
                    sh "envsubst < ./kubernetes/result-app.yaml | kubectl apply -n voting-app -f -"
                    //sh 'kubectl apply -f ./kubernetes/result-app.yaml -n voting-app'
                }
            }
        }
        stage('Deploy Worker App') {
            steps {
                script {
                    sh "envsubst < ./kubernetes/worker-app.yaml | kubectl apply -n voting-app -f -"
                    //sh 'kubectl apply -f ./kubernetes/worker-app.yaml -n voting-app'
                }
            }
        }
    }
    post {
        success {
            script {
                def successMessage = "Build #" + BUILD_NUMBER + " del pipeline " + env.JOB_NAME + " termino con exito el " + formatDate(currentBuild.getTimeInMillis())
                sh """
                    curl -X POST -H 'Content-type: application/json' --data '{"content": "${successMessage}"}' ${DISCORD_WEBHOOK_URL}
                """
            }
        }
        failure {
            script {
                def failureMessage = "Build #" + BUILD_NUMBER + " del pipeline " + env.JOB_NAME + " fallo el " + formatDate(currentBuild.getTimeInMillis())
                sh """
                    curl -X POST -H 'Content-type: application/json' --data '{"content": "${failureMessage}"}' ${DISCORD_WEBHOOK_URL}
                """
            }
        }
    }
}
