pipeline {
    agent any

    environment{
        IMAGE = 'praveenedward/static'
    }

    stages{
        stage('checout scm'){
            steps{
                checkout scm
            }
        }

        stage('build docker image'){
            steps{
                sh '''
                    docker build -t ${IMAGE}:v${BUILD_NUMBER} ./app
                '''
            }
        }

         stage('Docker Login') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'docker',
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {
                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login  -u "$DOCKER_USERNAME" --password-stdin
                    '''
                }
            }
        }

        stage('push to dockerhub'){
            steps{
                sh '''
                    docker push ${IMAGE}:v{BUILD_NUMBER}
                '''
            }
        }
        stage('Apply Kubernetes'){
            steps{
                sh '''
                    kubectl apply -f k8s/
                    kubectl get pods
                '''
            }
        }
    }
    

    post {

        success {
            echo "Deployment successful: ${IMAGE}:${IMAGE_VERSION}"
        }

        failure {
            echo "Deployment failed"
        }
    }

}