/* groovylint-disable-next-line CompileStatic */
pipeline {
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

        stage('Checkout SCM') {
            steps {
                script {
                    git credentialsId: 'gitmaster',
                    url: 'https://github.com/houseofposh01/-gitops_argocd_project',
                    branch: 'main'
                }
            }
        }
        stage('Build Image') {
            steps {
                script {
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push Image') {
            steps {
                script {
                    /* groovylint-disable-next-line NestedBlockDepth */
                    docker.withRegistry('', REGISTRY_CREDS) {
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('Remove Image') {
            steps {
                script {
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Deployment File') {
            steps {
                script {
                    sh """
                    cat deployment.yaml
                    sed -i 's+aaggroup/${APP_NAME}.*+aaggroup/${APP_NAME}:${IMAGE_TAG}+g' deployment.yaml
                    cat deployment.yaml
                    """
                }
            }
        }
        stage ('Git Push') {
            steps {
                script {
                    sh """
                        git config --global user.name "fola"
                        git config --global user.email "MrDevops@MrDevops.com"
                        git add .
                        git commit -m "Updated commit for build ${BUILD_NUMBER}"
                        """
                        withCredentials([gitUsernamePassword(credentialsId: 'gitmaster', gitToolName: 'Default')]) {
                            sh 'git push https://github.com/houseofposh01/-gitops_argocd_project main '
                        }
                }
            }
        }
        /*stage('Trigger ManifestUpdate') {
            steps {
                script {
                    echo 'triggering argoupdatejob'
                    build job: 'argoupdate', parameters: [string(name: 'DOCKERTAG', value: env.BUILD_NUMBER)]
                }
            }
        }*/
    }
}
