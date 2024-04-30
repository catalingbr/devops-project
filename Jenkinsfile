pipeline {

    agent any

    stages {

        stage('Checkout') {
            steps {
                git url: 'https://github.com/catalingbr/devops-project.git', branch: 'master'
            }

        }

        stage('Start backend') {
            steps {
                dir('backend') {
                    sh "npm install"
                    sh "nohup npm start &"
                    sleep 10
                    sh 'curl -k localhost:5000'
                }
            }
        }

        stage ('Start frontend') {
            steps {
                dir('frontend') {
                    sh "npm install"
                    sh "nohup npm start &"
                }
            }
        }

        // stage ('Build') {
        //     steps {
        //         sh "npm run build"
        //         sh "tar -czvf build.tar.gz build/*"
        //         archiveArtifacts artifacts: 'build.tar.gz', followSymlinks: false

        //     }
        // }

    }

    options {
        timestamps()
    }
}