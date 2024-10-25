pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "232183/my-survey-app:latest"
        DOCKER_CREDENTIALS_ID = 'docker_id'  // Use the correct credential ID (docker_id)
        GIT_REPO = 'https://github.com/AmartyaMaruth/SWE645.git'  // Replace with your GitHub repo URL
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig_id' // Kubernetes config credential ID
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
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Use kubeconfig for Kubernetes authentication
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG')]) {
                        // Apply the Kubernetes deployment YAML using kubectl
                        sh 'kubectl apply -f SWE645/k8s/my-survey-app-deployment.yaml --validate=false'
                    }
                }
            }
        }
    }
}
