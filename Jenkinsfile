pipeline {
    agent any
    tools {
        maven 'Maven3'  // Ensure Maven is installed
        jdk 'JD'       // Ensure JDK is installed
    }
    environment {
        DOCKERHUB_CREDENTIALS_ID = 'dockerhub-credentials'  // Credentials ID in Jenkins
        DOCKERHUB_REPO = 'muha270/localizedgreetingapp'      // Docker Hub repo
        DOCKER_IMAGE_TAG = 'latest'                          // Docker image tag
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/chuloz/LocalizedGreetingApp'
            }
        }
        stage('Run Tests') {
            steps {
                bat 'mvn clean test'  // For Windows agents
                // sh 'mvn clean test'  // Uncomment for Linux agents
            }
        }
        stage('Code Coverage') {
            steps {
                bat 'mvn jacoco:report'  // For Windows agents
                // sh 'mvn jacoco:report'  // Uncomment for Linux agents
            }
        }
        stage('Publish Test Results') {
            steps {
                junit '**/target/surefire-reports/*.xml'  // Publish JUnit results
            }
        }
        stage('Publish Coverage Report') {
            steps {
                jacoco()  // Publish Jacoco code coverage report
            }
        }
        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${env.DOCKERHUB_REPO}:${env.DOCKER_IMAGE_TAG}")  // Build Docker image
                }
            }
        }
        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', env.DOCKERHUB_CREDENTIALS_ID) {
                        docker.image("${env.DOCKERHUB_REPO}:${env.DOCKER_IMAGE_TAG}").push()  // Push image to Docker Hub
                    }
                }
            }
        }
    }
}
