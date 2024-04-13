pipeline {
    agent { label 'builder-node' }
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'main', description: '')
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
        stage('Create a directory') {
            steps {
                script {
                    sh """
                        cd /var/www/html
                        sudo mkdir articles
                    """ 
                }
            }
        }
        stage('Deploying the Code') {
            steps {
                script {
                    sh """
                        pwd
                        ls -l
                        sudo cp -r * /var/www/html/articles
                    """ 
                }
            }
        }
    }
}