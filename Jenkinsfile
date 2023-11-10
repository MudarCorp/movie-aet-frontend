pipeline {
    agent any
    
    environment {
        DOCKERHUB_USERNAME = "mudashir"
        APP_NAME = "movies-aet-frontend"
        IMAGE_TAG = "${BUILD_NUMBER}"
        IMAGE_NAME = "${DOCKERHUB_USERNAME}/${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }

    stages {
        stage('Clean up workspace') {
            steps {
                cleanWs()
            }
        }    

        stage('Checkout SCM') {
            steps {
                git credentialsId: 'github', url: 'https://github.com/MudarCorp/movie-aet-frontend.git', branch: 'master'
            }
        }

        stage('Install Dependencies') {
            steps {
                script {
                    // Install Node.js and npm
                    def nodejsInstallation = tool name: 'NodeJS 14', type: 'jenkins.plugins.nodejs.tools.NodeJSInstallation'
                    env.PATH = "${nodejsInstallation}/bin:${env.PATH}"
                    // Install project dependencies
                    sh 'npm install'
                }
            }
        }

        stage('Run Tests') {
            steps {
                script {
                    // Run your tests
                    sh 'npm test'
                }
            }
        }

        stage('Build') {
            steps {
                script {
                    // Run your build commands if needed
                    sh 'npm run build'
                }
            }
        }

        stage('Build Image') {
            steps {
                script {
                    def docker_image = docker.build("${IMAGE_NAME}:${IMAGE_TAG}")
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
