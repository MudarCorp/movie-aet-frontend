pipeline {
    agent any
    
    environment {
        DOCKER_HUB_CREDENTIALS = credentials('docker-hub')
    }
    
    stages {
        stage('Checkout') {
            steps {
                checkout([$class: 'GitSCM', branches: [[name: '*/master']], doGenerateSubmoduleConfigurations: false, extensions: [], userRemoteConfigs: [[url: 'https://github.com/MudarCorp/movie-gold-v1.git']]])
            }
        }
        
        stage('Build Frontend') {
            steps {
                sh 'npm install'
                sh 'npm run build'
            }
        }
        
        stage('Build Docker Image') {
            steps {
                script {
                    sh 'docker build -t mudashir/my-frontend-movie-app:1.0:${BUILD_NUMBER} .'
                }
            }
        }
        
        stage('Push Docker Image') {
            steps {
                script {
                    withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'DOCKER_HUB_CREDENTIALS', usernameVariable: 'DOCKER_USERNAME', passwordVariable: 'DOCKER_PASSWORD']]) {
                        sh "docker login -u ${DOCKER_USERNAME} -p ${DOCKER_PASSWORD}"
                    }
                    sh "docker push mudashir/my-frontend-movie-app:1.0:${BUILD_NUMBER}"
                }
            }
        }
        
        stage('Deploy to Kubernetes') {
            steps {
                script {
                    withCredentials([file(credentialsId: 'KUBE_CONFIG', variable: 'KUBE_CONFIG')]) {
                        sh 'kubectl apply -f Frontend Deployment.yaml --kubeconfig=$KUBE_CONFIG'
                    }
                }
            }
        }
    }
}
