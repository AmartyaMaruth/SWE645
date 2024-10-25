pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "232183/my-survey-app:latest"
        DOCKER_CREDENTIALS_ID = 'docker_id'  // Use the correct credential ID (docker_id)
        GIT_REPO = 'https://github.com/AmartyaMaruth/SWE645.git'  // Replace with your GitHub repo URL
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig_id' // Kubernetes config credential ID
        AWS_CREDENTIALS_ID = 'aws_credentials_id' // AWS credentials ID (for AWS credentials type)
    }
    
    stages {
        stage('Build Docker Image') {
            steps {
                script {
                    // Build the Docker image
                    sh "docker build -t ${DOCKER_IMAGE} ."
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    // Authenticate with Docker Hub using the credentials stored in Jenkins (docker_id)
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Login to Docker Hub
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        
                        // Push the Docker image to Docker Hub
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Fetch Kubernetes Deployment YAML') {
            steps {
                script {
                    // Remove any existing repo and clone the GitHub repository to fetch the YAML file
                    sh 'rm -rf SWE645'
                    sh 'git clone https://github.com/AmartyaMaruth/SWE645.git'
                    sh 'cd SWE645'
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use kubeconfig for Kubernetes authentication and AWS credentials
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG'),
                                     [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                        
                        // Apply the Kubernetes deployment YAML using kubectl
                        sh 'kubectl apply -f my-survey-app-deployment.yaml --validate=false'
                        sh 'kubectl apply -f my-survey-app-service.yaml --validate=false'
                    }
                }
            }
        }
    }
}
