pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Install npm') { 
            steps {
                sh 'npm install'
            }
        }

        stage('Start backend') {
            steps {
                dir('backend') {
                    sh "pwd"
                    sh "nohup npm start &"
                    sleep 10
                    sh 'curl -k localhost:5000'
                }
            }
        }

        stage ('Start frontend') {
            steps {
                dir('frontend') {
                    sh "pwd"
                    sh "cd ${WORKSPACE}/frontend & ls -l"
                    sh "nohup npm start &"
                }
            }
        }

        stage ('Build') {
            steps {
                sh "npm run build"
                sh "tar -czvf build.tar.gz build/*"
                archiveArtifacts artifacts: 'build.tar.gz', followSymlinks: false

            }
        }

    }

    options {
        timestamps()
    }
}