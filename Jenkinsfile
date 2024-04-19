pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Build Backend') { 
            steps {
                sh 'sudo docker build . -t backend-image:latest'
            }
        }

        stage('Build Frontend') {
            steps {
                sh 'sudo docker build ./frontend frontend-image:latest'
            }
        }

    }
}