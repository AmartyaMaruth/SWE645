pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    sh 'docker build -t 232183/my-survey-app:latest .'
                    sh 'docker push 232183 /my-survey-app:latest'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    sh 'kubectl apply -f my-survey-app-deployment.yaml'
                }
            }
        }
    }
}
