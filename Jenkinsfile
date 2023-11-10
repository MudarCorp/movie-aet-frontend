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
                    // Download and install a pre-built Node.js binary compatible with the system's GLIBC
                    sh 'curl -fsSL https://nodejs.org/dist/v14.17.6/node-v14.17.6-linux-x64.tar.xz | tar -xJ -C /opt/'
                    env.PATH = "/opt/node-v14.17.6-linux-x64/bin:${env.PATH}"
                    // Verify Node.js installation
                    sh 'node -v'
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
