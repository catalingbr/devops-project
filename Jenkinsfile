pipeline {

    agent any

    environment {
        CI = 'false'
        registry = 'catalingbr/devops-project'
        registryCredential = 'Dockerhub'
        dockerImage = ''
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Start backend') {
            steps {
                dir('backend') {
                    sh "ls -lrth"
                    sh "npm install"
                    sh "nohup npm start &"
                    sleep 10
                }
            }
        }

        stage('Start frontend') {
            steps {
                dir('frontend') {
                    sh "ls -lrth"
                    sh "npm install"
                    sh "nohup npm start &"
                }
            }
        }


        stage('Build') {
            steps {
                sh "npm run build"
                sh "tar -czvf build.tar.gz ./frontend ./backend package.json"
                archiveArtifacts artifacts: 'build.tar.gz', followSymlinks: false
            }
        }

        stage('Test') {
            steps {
                sh 'curl -k -vvvv localhost:5000'
            }
        }

        stage('Build Docker image') {
            steps {
                script {
                    dockerImage = docker.build(registry + ":$BUILD_NUMBER")
                }
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }

    options {
        timestamps()
    }
}