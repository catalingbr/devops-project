pipeline {

    agent any

    environment {
        CI = 'false'
        backendRegistry = 'registry.hub.docker.com/catalingbr/ecommerce-backend'
        frontendRegistry = 'registry.hub.docker.com/catalingbr/ecommerce-frontend'
        backendDockerImage = ''
        frontendDockerImage = ''
    }

    stages {

        stage('Clean Workspace') {
            steps {
                cleanWs()
            }
        }

        stage('Git Checkout') {
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
                    sleep 10
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

        stage('Run tests') {
            steps {
                sh "curl -vvv localhost:5000"
                sh "curl -vvv localhost:5000/register"

                sh "curl -vvv localhost:3000"
            }
        }

        stage('Stop npm processes') {
            steps {
                script {
                    sh '''
                        sudo lsof -i:5000
                        sudo kill $(sudo lsof -t -i:5000)

                        sudo lsof -i:3000
                        sudo kill $(sudo lsof -t -i:3000)
                    '''
                }
            }
        }

        stage('Build Docker Images') {
            steps {
                script {
                    backendDockerImage = docker.build(backendRegistry + ":${env.BUILD_NUMBER}", "-f Dockerfile .")
                    dir('frontend'){
                        frontendDockerImage = docker.build(frontendRegistry + ":${env.BUILD_NUMBER}", "-f Dockerfile .")
                    }
                    
                }
            }
        }

        stage('Upload Docker Images') {
            steps {
                script {
                    try {
                        docker.withRegistry("https://registry.hub.docker.com", "DOCKERHUB_CREDENTIALS") {
                            backendDockerImage.push("${env.BUILD_NUMBER}")
                            backendDockerImage.push("latest")

                            frontendDockerImage.push("${env.BUILD_NUMBER}")
                            frontendDockerImage.push("latest")

                            echo "Docker images pushed successfully"
                        }
                    } catch (Exception e) {
                        echo "Error occurred while pushing Docker images: ${e.message}"
                    }   
                }
            }
        }

        stage('Start containers with docker-compose') {
            steps {
                script{ 
                    sh "docker-compose up -d"

                    sh "docker ps -a"

                    sleep 25
                    
                    sh "curl -vvv localhost:5000"
                    sh "curl -vvv localhost:5000/register"
                    sh "curl -vvv localhost:3000"
                }

            }

            post {
                always {
                    sh "docker rmi -f ${backendRegistry}:${env.BUILD_NUMBER}"
                    sh "docker rmi -f ${frontendRegistry}:${env.BUILD_NUMBER}"

                    sh "docker-compose down"

                    sh "docker system prune -f"
                    sh "docker volume prune -f"
                }
            }
        }
    }

    post {
        failure {
            emailext to: 'catalingabriel.nicolae@gmail.com', subject: "Build Failed: ${currentBuild.fullDisplayName}", body: "Please check the build logs."
        }

        success {
            emailext to: 'catalingabriel.nicolae@gmail.com', subject: "Build Succeeded: ${currentBuild.fullDisplayName}", body: "Build is successful."
        }

        always {
            cleanWs()
        }
    }

    options {
        timestamps()
    }

    triggers {
        githubPush()
    }
}
