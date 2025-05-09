pipeline {
    agent {
        docker {
            image 'docker:latest'
            args '-v /var/run/docker.sock:/var/run/docker.sock'
        }
    }

    environment {
        DOCKER_HUB_USERNAME = "s8kevinaf02"
        ALPHA_APPLICATION_01_REPO = "app-01"
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: '')
        string(name: 'APP1_TAG', defaultValue: 'app1.1.0', description: '')
        string(name: 'PORT_ON_DOCKER_HOST_01', defaultValue: '3000', description: '')
        string(name: 'CONTAINER_NAME', defaultValue: 'app-01', description: '')
    }

    stages {
        stage('Clone Repository') {
            steps {
                checkout scm
            }
        }

        stage('Checking the code') {
            steps {
                sh 'echo Hello from Linux agent'
            }
        }

        stage('Building application 01') {
            steps {
                sh """
                    docker build -t ${DOCKER_HUB_USERNAME}/${ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG} .
                    docker images | grep ${params.APP1_TAG}
                """
            }
        }

        stage('Login to Docker Hub') {
            steps {
                withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', 
                                                  usernameVariable: 'DOCKER_HUB_USERNAME', 
                                                  passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                    sh """
                        echo "$DOCKER_HUB_PASSWORD" | docker login -u "$DOCKER_HUB_USERNAME" --password-stdin
                    """
                }
            }
        }

        stage('Pushing images to Docker Hub') {
            steps {
                sh "docker push ${DOCKER_HUB_USERNAME}/${ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}"
            }
        }

        stage('Deploying the application 01') {
            steps {
                sh """
                    docker run -itd -p ${params.PORT_ON_DOCKER_HOST_01}:80 --name ${params.CONTAINER_NAME} ${DOCKER_HUB_USERNAME}/${ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
                    docker ps | grep ${params.CONTAINER_NAME}
                """
            }
        }
    }
}
