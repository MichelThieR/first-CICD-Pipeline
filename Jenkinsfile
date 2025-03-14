pipeline {
    agent any
    
    environment {
        DOCKER_IMAGE = "mtrose/flask-app"
        DOCKER_CREDENTIALS = "michel-dockerhub" // Jenkins credentials ID for DockerHub
        CONTAINER_NAME = "flask-app"
    }
    
    stages {
        stage('Michel - Build Docker Image') {
            steps {
                script {
                    echo "Building Docker image..."
                    sh "sudo docker build -t $DOCKER_IMAGE ."
                }
            }
        }
        
        stage('Michel - Login to Dockerhub') {
            steps {
                script {
                    echo "Logging in to DockerHub..."
                    withCredentials([usernamePassword(credentialsId: env.DOCKER_CREDENTIALS, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        sh "echo $DOCKER_PASS | sudo docker login -u $DOCKER_USER --password-stdin"
                    }
                }
            }
        }
        
        stage('Michel - Push image to Dockerhub') {
            steps {
                script {
                    echo "Pushing Docker image to DockerHub..."
                    sh "sudo docker push $DOCKER_IMAGE"
                }
            }
        }

        stage('Run Docker Container') {
            steps {
                script { // check if container exist, then delete and create with updated image
                    sh '''
                        if [ $(sudo docker ps -q -f name=$CONTAINER_NAME) ]; then
                            sudo docker stop $CONTAINER_NAME
                            sudo docker rm $CONTAINER_NAME
                        fi
                    '''
                    sh 'sudo docker run -d -p 5000:5000 --name $CONTAINER_NAME $DOCKER_IMAGE' // expose port 5000 of server                  
                }
            }
        }
    }
    post {
        always {
            sh "sudo docker logout"
        }
    }

}
