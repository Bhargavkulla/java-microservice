pipeline {
    agent any

    environment {
        DOCKER_IMAGE = "java-microservice"
        DOCKER_REGISTRY = "bhargavkulla"
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'develop', url: 'https://github.com/Bhargavkulla/java-microservice.git'
            }
        }

        stage('Build & Test') {
            steps {
                // Make sure Maven is executed in the correct directory
                dir('java-microservice') { 
                    sh 'mvn clean install -DskipTests=false'
                }
            }
        }

        stage('Docker Build & Push') {
            steps {
                script {
                    sh 'docker build -t $DOCKER_REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER .'
                    sh 'docker push $DOCKER_REGISTRY/$DOCKER_IMAGE:$BUILD_NUMBER'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    sh 'kubectl apply -f deployment.yaml'
                    sh 'kubectl apply -f service.yaml'
                }
            }
        }
    }
}
