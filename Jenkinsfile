pipeline {
    agent any

    environment {
        DOCKER_IMAGE_NAME = 'fastapi_devops_i'
        DOCKER_CONTAINER_NAME = 'fastapi_devops_c'
        HOST_PORT = '5000'
        CONTAINER_PORT = '5000'
    }

    stages {
        stage('git checkout') {
            steps {
                deleteDir()
                checkout scm
            }
        }

        stage('stop older container and auto remove')
        {
            steps{
                script{
                    def doc_containers = sh(returnStdout: true, script: 'docker container ps -aq').replaceAll('\n', ' ') 
                    if (doc_containers) {
                        sh "docker stop ${doc_containers}"
                    }
                    
                }
            }
        }

        stage('Build Image') {
            steps {
                echo 'Building..'
                echo "build number :${env.BUILD_ID}"
                sh "docker build -t ${DOCKER_IMAGE_NAME}:${env.BUILD_ID} ."
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
                sh "docker run -d --rm -p ${HOST_PORT}:${CONTAINER_PORT} --name ${DOCKER_CONTAINER_NAME} ${DOCKER_IMAGE_NAME}:${env.BUILD_ID}"
            }
        }
        stage('Clean Older Images')
        {
            steps {
                sh 'docker image prune -a -f'
            }
        }
    }

    post {
        always {
            echo 'This will always run'
        }
        success {
            echo 'Build Successful'
        }
        failure {
            echo 'Build Failed'
        }
        unstable {
            echo 'Build unstable'
        }
        changed {
            echo 'This will run only if the state of the Pipeline has changed'
            echo 'For example, if the Pipeline was previously failing but is now successful'
        }
    }
}