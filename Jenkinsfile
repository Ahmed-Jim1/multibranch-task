@Library("shared-library@main") _  // Import the shared library

pipeline {
    agent { label 'slave01' }

    environment {
        DOCKER_IMAGE_NAME = 'ivolve'
        DOCKER_HUB_REPO = 'ahmedmahmood44'
        DOCKER_HUB_CREDENTIALS = credentials('Docker')
        NAMESPACE = ""
    }

    stages {
        stage('Set Namespace') {
            steps {
                script {
                    // Get the appropriate namespace based on the branch name
                    NAMESPACE = setNamespace(env.BRANCH_NAME)
                    echo "Deploying to namespace: ${NAMESPACE}"
                }
            }
        }
        
        stage('Checkout Code') {
            steps {
                echo "Checking out code from SCM..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerTasks.buildDockerImage(DOCKER_IMAGE_NAME, env.BUILD_NUMBER)
                }
            }
        }

        stage('Docker Login') {
            steps {
                script {
                    dockerTasks.dockerLogin(DOCKER_HUB_CREDENTIALS_USR, DOCKER_HUB_CREDENTIALS_PSW)
                }
            }
        }

        stage('Push to Docker Hub') {
            steps {
                script {
                    dockerTasks.pushDockerImage(DOCKER_IMAGE_NAME, env.BUILD_NUMBER, DOCKER_HUB_REPO)
                }
            }
        }

        stage('Deploy to Kubernetes') {
            steps {
                script {
                    // Deploy to Kubernetes using the shared library
                    deployToKubernetes(NAMESPACE)
                }
            }
        }
    }

    post {
        always {
            echo "Cleaning up Docker resources..."
            script {
                dockerTasks.dockerCleanup()
            }
        }
        success {
            echo "Pipeline executed successfully!"
        }
        failure {
            echo "Pipeline failed. Check the logs for more details."
        }
    }
}
