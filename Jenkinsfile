pipeline {

    agent any

    environment {
        CI = 'false'
        backendRegistry = 'catalingbr/ecommerce-backend'
        frontendRegistry = 'catalingbr/ecommerce-frontend'
        registryCredential = credentials('DOCKERHUB_CREDENTIALS')
        backendDockerImage = ''
        frontendDockerImage = ''
    }

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Start Backend') {
            steps {
                dir('backend') {
                    sh "ls -lrth"
                    sh "npm install"
                    sh "nohup npm start &"
                    sleep 10
                }
            }
        }

        stage('Start Frontend') {
            steps {
                dir('frontend') {
                    sh "ls -lrth"
                    sh "npm install"
                    sh "nohup npm start &"
                }
            }
        }


        stage('Build & Arhive') {
            steps {
                sh "npm run build"
                sh "tar -czvf build.tar.gz ./frontend ./backend package.json"
                archiveArtifacts artifacts: 'build.tar.gz', followSymlinks: false
            }
        }

        stage('Test') {
            steps {
                sh 'curl -k -vv localhost:5000'
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    backendDockerImage = docker.build(backendRegistry + ":${env.BUILD_NUMBER}", "-f Dockerfile-backend .")
                }
            }
        }

        stage('Upload Docker Images') {
            steps {
                script {
                    try {
                        docker.withRegistry("https://registry.hub.docker.com", "registryCredential") {
                            backendDockerImage.push("${env.BUILD_NUMBER}")
                            backendDockerImage.push("latest")

                            echo "Docker images pushed successfully"
                        }
                    } catch (Exception e) {
                        echo "Error occurred while pushing Docker images: ${e.message}"
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