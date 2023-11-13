pipeline {
    agent any
    
    environment {
        DOCKERHUB_USERNAME = "mudashir"
        APP_NAME = "movies-aet-frontend"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    tools {
        Nodejs 20 “Nodejs 20” //name in “” should be similar to name used for installer in the global tool configuration.
    }
    stages {
        stage(‘Clean up workspace’) {
            steps {
                cleanWs()
            }
        }
        stage(‘Checkout SCM’) {
            steps {
                git credentialsId: ‘github’, url: ‘https://github.com/MudarCorp/movie-aet-frontend.git’, branch: ‘master’
            }
        }
        stage(‘Build App’) {
            steps {
                script {
                    // Change directory to your Node.js application’s directory
                    dir(‘./’) {
                        // Install dependencies and build the application
                        sh ‘npm install’
                    }
                }
            }
        }
        stage(‘Build Image’) {
            steps {
                script {
                    docker_image = docker.build(“${IMAGE_NAME}:${IMAGE_TAG}“)
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker.withRegistry('', REGISTRY_CREDS) {
                        docker_image.push("${BUILD_NUMBER}")
                        docker_image.push('latest')
                    }
                }
            } 
        }

        stage('Remove Images') {
            steps {
                sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                sh "docker rmi ${IMAGE_NAME}:latest"
            }
        }
    }
}
