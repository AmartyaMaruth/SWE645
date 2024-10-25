pipeline {
    agent any
    stages {
        stage('Build') {
            steps {
                script {
                    // Build the Docker image and tag it
                    sh 'docker build -t 232183/my-survey-app:latest .'
                    
                    // Push the Docker image to the Docker registry
                    sh 'docker push 232183/my-survey-app:latest'
                }
            }
        }
        stage('Deploy') {
            steps {
                script {
                    // Deploy the Docker container to Kubernetes using kubectl
                    sh 'kubectl apply -f my-survey-app-deployment.yaml'
                }
            }
        }
    }
}
