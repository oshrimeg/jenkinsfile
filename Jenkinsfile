pipeline {
    agent any

    environment {
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        DOCKER_IMAGE_NAME = 'oshrimeg/jenkins-project'
        DOCKER_REGISTRY = 'docker.io'
        BUILD_ID = 'Latest'
    }

    stages {
        stage('Clone Repository') {
            steps {
                git 'https://github.com/oshrimeg/jenkins-project.git'
            }
        }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKER_IMAGE_NAME}:${BUILD_ID}")
                }
            }
        }

        stage('Push Docker Image') {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1/", "${DOCKER_CREDENTIALS_ID}") {
                        // Push both latest and specific build ID
                        docker.image("${DOCKER_IMAGE_NAME}:${BUILD_ID}").push('latest')
                        docker.image("${DOCKER_IMAGE_NAME}:${BUILD_ID}").push("${BUILD_ID}")
                    }
                }
            }
        }

        stage('Deploy Application') {
            steps {
                script {                   
                    bat 'docker-compose -f compose.yaml up -d'
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}
