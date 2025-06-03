pipeline {
    agent {
        docker {
            image 'maven:3.8.6-openjdk-21-slim' // Maven + JDK 21
            args '-v /var/run/docker.sock:/var/run/docker.sock' // para usar Docker desde dentro del contenedor
        }
    }
    environment {
        DOCKER_IMAGE = 'paupulpeg/microservice-app'
    }
    stages {
        stage('Build JAR') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    TAG = sh(script: 'date "+%d%m%Y-%H%M%S"', returnStdout: true).trim()
                    docker.build("${DOCKER_IMAGE}:${TAG}")
                    docker.image("${DOCKER_IMAGE}:${TAG}").tag('latest')
                }
            }
        }
        stage('Push Docker Image') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-creds', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD')]) {
                    sh "docker login -u $DOCKER_USERNAME -p $DOCKER_PASSWORD"
                    sh "docker push ${DOCKER_IMAGE}:${TAG}"
                    sh "docker push ${DOCKER_IMAGE}:latest"
                }
            }
        }
        stage('Cleanup') {
            steps {
                sh "docker image rm ${DOCKER_IMAGE}:${TAG} ${DOCKER_IMAGE}:latest || true"
            }
        }
    }
}
