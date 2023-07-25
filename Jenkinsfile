pipeline{
    agent any 


environment{
    DOCKERHUB_USERNAME = "inodinwetrust10"
    APP_NAME = "gitops"
    IMAGE_TAG = "${BUILD_NUMBER}"
    IMAGE_NAME = "${DOCKERHUB_USERNAME}"+"/"+"${APP_NAME}"
    REGISTRY_CRED = 'dockerhub'
}

    stages{
        stage('Cleanup workspace'){
            steps{
                script{
                    cleanWs()
                }
            }
        }
        stage('Checkout SCM'){
            steps{
                script{
                    git credentialsId:'github',
                    url:'https://github.com/inodinwetrust10/gitops.git',
                    branch:'main'
                }
            }
        }
        stage('Build docker image'){
            steps{
                script{
                    docker_image = docker.build "${IMAGE_NAME}"
                }
            }
        }
        stage('Push image to dockerhub'){
            steps{
                script{
                    docker.withRegistry('',REGISTRY_CRED){
                        docker_image.push("$BUILD_NUMBER")
                        docker_image.push('latest')
                    }
                }
            }
        }
        stage('DELETE DOCKER IMAGES'){
            steps{
                script{
                    sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                    sh "docker rmi ${IMAGE_NAME}:latest"
                }
            }
        }
        stage('Updating kubernetes deployment file'){
            steps{
                script{
                    sh """
                    cat deployment.yml
                    sed -i 's/${APP_NAME}.*/${APP_NAME}:${IMAGE_TAG}/g deployment.yml'
                    cat deployment.yml
                    """
                }
            }
        }
    }
}