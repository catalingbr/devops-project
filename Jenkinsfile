pipeline {
    agent any
    stages {
        stage('Build backend') { 
            steps {
                sh '''
                cd backend
                npm install
                ''' 
            }
        }
    }
}