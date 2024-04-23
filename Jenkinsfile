pipeline {

    agent any

    environment {
        DOCKERHUB_REGISTRY = 'catalingbr/devops-project'
        DOCKERHUB_CREDENTIALS = credentials('DOCKERHUB_CREDENTIALS')
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Build Backend Image') { 
            steps {
                script {
                   backendDockerImage = docker.build('frontend-image:latest', '-f Dockerfile-backend .')
                }
            }
        }

        stage('Build Frontend') {
            steps {
                script {
                    frontendDockerImage = docker.build('frontend-image:latest', '-f Dockerfile ./frontend')
                }
            }
        }

    }

    options {
        timestamps()
    }
}