pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "mtrose/flask-app"
        DOCKER_CREDENTIALS = "michel-dockerhub" // Jenkins credentials ID for DockerHub
    }
    
    stages {
        stage('Michel - Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "docker build -t $DOCKER_IMAGE ."
                }
            }
        }
        
        stage('Michel - Login to Dockerhub') {
            steps {
                script {
                    echo "Logging in to DockerHub..."
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }
        
        stage('Michel - Push image to Dockerhub') {
            steps {
                script {
                    echo "Pushing Docker image to DockerHub..."
                    sh "docker push $DOCKER_IMAGE"
                }
            }
        }
    }
    post {
        always {
            sh "docker logout"
        }
    }

}
