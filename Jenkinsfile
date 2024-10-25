pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "232183/my-survey-app:latest"
        DOCKER_CREDENTIALS_ID = 'docker_id'  // Use the correct credential ID (docker_id)
        GIT_REPO = 'https://github.com/AmartyaMaruth/SWE645.git'  // Replace with your GitHub repo URL
       // GIT_CREDENTIALS_ID = 'git_id'  // Use the correct GitHub credential ID
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
                    // Clone the GitHub repository or fetch the YAML file
                    
                        sh 'git clone https://github.com/AmartyaMaruth/SWE645.git'
                        sh 'cp survey-app/k8s/my-survey-app-deployment.yaml .'
                    
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using kubectl
                    sh 'kubectl apply -f my-survey-app-deployment.yaml'
                }
            }
        }
    }
}
