pipeline {
    agent any
    environment {
        DOCKER_IMAGE = "232183/my-survey-app:latest"
        DOCKER_CREDENTIALS_ID = 'docker_id'  // Use the correct credential ID for Docker Hub
        GIT_REPO = 'https://github.com/AmartyaMaruth/SWE645.git'  // GitHub repo URL
        KUBECONFIG_CREDENTIALS_ID = 'kubeconfig_id' // Kubernetes config credential ID
        AWS_CREDENTIALS_ID = 'aws_credentials_id' // AWS credentials ID (for AWS credentials type)
    }
    
    stages {
        stage('Clone Git Repository') {
            steps {
                script {
                    // Remove any existing repo and clone the GitHub repository
                    echo 'Cloning Git repository...'
                    sh 'rm -rf SWE645' // Remove any existing directory
                    sh 'git clone ${GIT_REPO}' // Clone the repository
                    dir('SWE645') {
                        echo 'Repository cloned, now proceeding with build.'
                    }
                }
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    echo 'Building Docker image...'
                    dir('SWE645') {
                        // Build the Docker image using the Dockerfile
                        sh 'docker build -t ${DOCKER_IMAGE} .'
                    }
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    echo 'Pushing Docker image to Docker Hub...'
                    withCredentials([usernamePassword(credentialsId: DOCKER_CREDENTIALS_ID, usernameVariable: 'DOCKER_USER', passwordVariable: 'DOCKER_PASS')]) {
                        // Login to Docker Hub
                        sh 'echo $DOCKER_PASS | docker login -u $DOCKER_USER --password-stdin'
                        
                        // Push the Docker image to Docker Hub
                        sh "docker push ${DOCKER_IMAGE}"
                    }
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    echo 'Deploying to Kubernetes...'
                    withCredentials([file(credentialsId: KUBECONFIG_CREDENTIALS_ID, variable: 'KUBECONFIG'),
                                     [$class: 'AmazonWebServicesCredentialsBinding', credentialsId: AWS_CREDENTIALS_ID]]) {
                        
                        // Delete any existing resources (if any)
                        sh 'kubectl delete -f my-survey-app-deployment.yaml'
                        
                        // Apply the deployment to Kubernetes
                        sh 'kubectl apply -f my-survey-app-deployment.yaml --validate=false'
                        
                        // Uncomment and modify if needed for service file
                        // sh 'kubectl apply -f my-survey-app-service.yaml --validate=false'
                    }
                }
            }
        }
    }

    post {
        failure {
            echo 'Pipeline failed. Please check the logs for errors.'
        }
        success {
            echo 'Pipeline completed successfully!'
        }
    }
}
