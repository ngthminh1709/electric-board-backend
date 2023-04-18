pipeline {
    agent any
    environment {
        registryUrl = "https://index.docker.io/v1/"
        credentialsId = "DOCKER_HUB"
        DOCKER_HUB = credentials('DOCKER_HUB')
        dockerImageName = "electric-board-backend"
        dockerfilePath = "./docker"
    }
    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Get version') {
            steps {
                script {
                    VERSION = sh(returnStdout: true, script: "cat package.json | jq -r '.version'").trim()
                    echo "Version: $VERSION"
                }
            }
        }

        stage('Compress Code') {
            steps {
                sh 'chmod +x ./compress.sh && ./compress.sh'
            }
        }

        stage('Build and Push Docker Image') {
            steps {
                sh 'echo $DOCKERHUB_PSW |  docker login -u $DOCKERHUB_USR --password-stdin'
                script {
                    withDockerRegistry([credentialsId: credentialsId, url: registryUrl]) {
                        // Đăng nhập Docker registry trước khi build và push image
                        def dockerImage = docker.build("ngthminhdev/electric-board-backend:${VERSION}", "./docker")
                        dockerImage.push()
                    }
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}