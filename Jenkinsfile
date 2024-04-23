pipeline {
    agent { label 'builder-node' }

    environment {
        DOCKER_HUB_USERNAME = "s8kevinaf02"
        ALPHA_APPLICATION_01_REPO = "article_app"
        
    }

    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: '')
        string(name: 'APP1_TAG', defaultValue: 'app.01', description: '')
        string(name: 'PORT_ON_DOCKER_HOST_01', defaultValue: '3000', description: '')
        
    }
    stages {
        stage('Clone Repository') {
            steps {
                script {
                    //Clone the repository using SSH with private key and checkout the specific branch
                    git credentialsId: 'jenkins-agent-node',
                        url: 'https://github.com/s8kevinaf02/Articles.git',
                        branch: "${params.BRANCH_NAME}"
                }
            }
        }
        stage('Building application 01') {
            steps {
                script {
                    dockerBuild("${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}", ".")
                    dockerImagesCheck(params.APP1_TAG)
                }
            }
        }
        stage('Login to Docker Hub') {
            steps {
                script {
                    withCredentials([usernamePassword(credentialsId: 'docker-hub-credentials', 
                                                      usernameVariable: 'DOCKER_HUB_USERNAME', 
                                                      passwordVariable: 'DOCKER_HUB_PASSWORD')]) {
                        sh """
                            echo ${env.DOCKER_HUB_PASSWORD} | docker login -u ${env.DOCKER_HUB_USERNAME} --password-stdin
                        """
                    }
                }
            }
        }
        stage('Pushing images to Docker Hub') {
            steps {
                script {
                    dockerPush("${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}")
                }
            }
        }

        stage('Deploying the article application 01') {
            steps {
                script {
                    try {
                        dockerRun("app-01", params.PORT_ON_DOCKER_HOST_01, "${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}")
                        dockerPS("app-01")
                    } catch (Exception e) {
                        handleDeploymentError(e, "01")
                    }
                }
            }
        }
    }
}