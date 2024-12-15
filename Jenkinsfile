@Library("shared-library@main") _  // Import the shared library

pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'ivolve'
        DOCKER_HUB_REPO = 'ahmedmahmood44'
        DOCKER_HUB_CREDENTIALS = credentials('Docker')
        OPENSHIFT_PROJECT = 'ahmedmahmoud'
        OPENSHIFT_SERVER = 'https://api.ocp-training.ivolve-test.com:6443'
        OPENSHIFT_TOKEN = 'sha256~52gqjo6YtbYK20vBfi7eN_G35XjByjrhyF3fb5V9yec'
    }

    stages {
        stage('Checkout Code') {
            steps {
                echo "Checking out code from SCM..."
                checkout scm
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    dockerTasks.buildDockerImage(DOCKER_IMAGE_NAME, env.BUILD_NUMBER, './Jenkins/Task-3')
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

        stage('Deploy to OpenShift') {
            steps {
                script {
                    openshiftTasks.loginOpenShift(OPENSHIFT_SERVER, OPENSHIFT_TOKEN)
                    openshiftTasks.deployToOpenShift('./Jenkins/Task-3')
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
