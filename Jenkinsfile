pipeline{
    
    agent any

    environment {
        DOCKERHUB_USERNAME = 'aaggroup'
        APP_NAME = 'gitops-argo-app'
        IMAGE_TAG = "${BUILD_NUMBER}"
        /* groovylint-disable-next-line ConsecutiveStringConcatenation */
        IMAGE_NAME = "${DOCKERHUB_USERNAME}" + '/' + "${APP_NAME}"
        REGISTRY_CREDS = 'dockerhub'
    }
    stages {
        stage('Cleanup Workspace') {
            steps {
                script {
                    cleanWs()
                }
            }
        }

        /*stage('Checkout SCM') {
            steps {
                script {
                    git credentialsId: 'gitmaster',
                    url: 'https://github.com/houseofposh01/gitops_project_argo.git',
                    branch: 'main'
                }
            }
        }*/
        stage('Build Image') {
            steps {
                script {
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
    }
}