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

        stage('Test application') {
            steps {
                sh "cd ${WORKSPACE}/backend & ls -l"
                sh "npm start"

                sh "cd ${WORKSPACE}/frontend & ls -l"
                sh "nohup npm start &"

                sleep 10
                
                sh 'curl -k localhost:5000'
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