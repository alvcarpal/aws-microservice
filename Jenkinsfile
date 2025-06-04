pipeline {
    agent any

    environment {
        TAG = sh(returnStdout: true, script: 'date "+%d%m%Y-%H%M%S"').trim()
        DOCKER_IMAGE = 'Paula1670/microservice-app'
    }

    stages {
        stage("Clone Git Repository") {
            steps {
                git(
                    url: "https://github.com/alvcarpal/aws-microservice.git",
                    branch: "main",
                    changelog: true,
                    poll: true
                )
            }
        }

        stage('Build Docker Image') {
            steps {
                sh """
                echo "Building Docker image..."
                docker build -t ${DOCKER_IMAGE}:${TAG} .
                docker tag ${DOCKER_IMAGE}:${TAG} ${DOCKER_IMAGE}:latest
                """
            }
        }

        stage('Test') {
            steps {
                echo 'Testing... (agrega aquí tus tests)'
            }
        }

        stage('Publish') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub', usernameVariable: 'paupulpeg', passwordVariable: 'Se166uS394')]) {
                    sh """
                    echo "Logging in to Docker Hub..."
                    docker login -u="${USERNAME}" -p="${PASSWORD}"
                    echo "Pushing images..."
                    docker push ${DOCKER_IMAGE}:${TAG}
                    docker push ${DOCKER_IMAGE}:latest
                    """
                }
            }
        }

        stage('Clean') {
            steps {
                sh """
                echo "Cleaning up local images..."
                docker rmi ${DOCKER_IMAGE}:${TAG} ${DOCKER_IMAGE}:latest || true
                """
            }
        }

        stage('Deploy') {
            steps {
                echo 'Deploying... (agrega aquí tu lógica de despliegue)'
            }
        }
    }
}
