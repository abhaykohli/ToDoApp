pipeline {
    agent any
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/abhaykohli/ToDoApp.git'
            }
        }
        
        stage('Build and Push Docker Image') {
            environment {
                DOCKER_IMAGE = "abhaykohli/devops_practice:${BUILD_NUMBER}"
                REGISTRY_CREDENTIALS = credentials('docker-creds')
            }
            steps {
                script {
                    docker.build("${DOCKER_IMAGE}", ".")
                    docker.withRegistry('https://index.docker.io/v1/', REGISTRY_CREDENTIALS) {
                        docker.image("${DOCKER_IMAGE}").push()
                    }
                }
            }
        }
        
        stage('Checkout K8S manifest SCM') {
            steps {
                git branch: 'main', url: 'https://github.com/abhaykohli/ToDoApp.git'
            }
        }
        
        stage('Update K8S manifest & push to Repo') {
            steps {
                script {
                    withCredentials([gitUsernamePassword(credentialsId: 'GIT_USERNAME', gitToolName: 'Default')]) {
                        sh '''
                            cd deploy
                            cat deploy.yaml
                            sed -i "s/ReplaceImageTag/${BUILD_NUMBER}/g" deploy.yaml
                            cat deploy.yaml
                            git add deploy.yaml
                            git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
                            git remote -v
                            git push https://github.com/abhaykohli/ToDoApp.git HEAD:main
                        '''
                    }
                }
            }
        }
    }
}
