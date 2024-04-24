pipeline {
    agent any

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
                sh "ls -l"
            }
        }

        stage('Building application 01') {
            steps {
                script {
                    sh """
                        docker build -t ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG} .
                        docker images |grep ${params.APP1_TAG}
                    """ 
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
                    sh """
                        docker push ${env.DOCKER_HUB_USERNAME}/${env.ALPHA_APPLICATION_01_REPO}:${params.APP1_TAG}
                    """
                }
            }
        }

        stage('Deploying the application 01') {
            steps {
                script {
                   sh """
                         docker run -itd -p ${params.PORT_ON_DOCKER_HOST}:80 --name ${params.CONTAINER_NAME} ${params.IMAGE_NAME}
                         docker ps |grep ${params.CONTAINER_NAME}
                     """
                }
            }
        }
    }
}    

//     post {
//         always {
//             script {
//                 cleanUpContainers("app-contain-01")
//                 cleanUpContainers("app-contain-02")
//             }
//         }
//         success{
//             echo "pipeline succeed"
//         }
//     }
// }

// def dockerBuild(imageTag, dockerFile) {
//     sh "docker build -t ${imageTag} ${dockerFile}"
// }

// def dockerImagesCheck(tag) {
//     sh "docker images | grep ${tag}"
// }

// def dockerPush(imageTag) {
//     sh "docker push ${imageTag}"
// }

// def dockerRun(containerName, hostPort, imageTag) {
//     sh """
//         docker rm -f ${containerName} || true
//         docker run -itd -p ${hostPort}:80 --name ${containerName} ${imageTag}
//     """
// }

// def dockerPS(containerName) {
//     sh "docker ps | grep ${containerName}"
// }

// def handleDeploymentError(Exception e, String appNumber) {
//     sh """
//         echo "Error deploying application ${appNumber}: ${e.message}"
//         docker logs app-contain-${appNumber}
//         exit 1
//     """
// }

// def cleanUpContainers(containerName) {
//     sh "docker rm -f ${containerName} || true"
// }
