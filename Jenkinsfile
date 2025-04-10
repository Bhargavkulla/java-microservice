pipeline {
    agent any

    environment {
        REGISTRY = 'docker.io'
        IMAGE_NAME = 'bhargavakulla/java-microservice'
        SONARQUBE_ENV = 'MySonarQube' // Make sure this matches Jenkins config
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('SonarQube Analysis') {
            when {
                anyOf {
                    branch pattern: "feature/.*", comparator: "REGEXP"
                    branch "develop"
                    branch "master"
                }
            }
            steps {
                withSonarQubeEnv("${SONARQUBE_ENV}") {
                    sh 'mvn sonar:sonar'
                }
            }
        }

        stage('Docker Build & Push') {
            when {
                anyOf {
                    branch "develop"
                    branch "master"
                }
            }
            steps {
                withCredentials([usernamePassword(credentialsId: 'dockerhub-cred', usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                    sh """
                        echo "$DOCKER_PASS" | docker login -u "$DOCKER_USER" --password-stdin
                        docker build -t $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER .
                        docker push $REGISTRY/$IMAGE_NAME:$BUILD_NUMBER
                    """
                }
            }
        }

        stage('Deploy to Kubernetes') {
            when {
                branch "master"
            }
            steps {
                sh 'kubectl apply -f deployment.yaml'
                sh 'kubectl apply -f service.yaml'
            }
        }
    }
}

