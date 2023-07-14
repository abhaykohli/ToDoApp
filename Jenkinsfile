pipeline {
    
    agent any 
    
    environment {
        IMAGE_TAG = "${BUILD_NUMBER}"
    }
    
    stages {
        
        stage('Checkout'){
           steps {
              git branch: 'main', url: 'https://github.com/abhaykohli/ToDoApp.git'
           }
        }

        stage('Build Docker Image'){
            steps{
                script{
                    sh '''
                    echo 'Buid Docker Image'
                    docker build -t abhaykohli/devops_practice:${BUILD_NUMBER} .
                    '''
                }
            }
        }

		stage('Push the artifacts') {
				steps {
					withDockerRegistry(credentialsId: 'docker-creds', url: 'https://index.docker.io/v1/') {
					script {
					sh '''
					echo 'Push to Repo'
					docker push abhaykohli/devops_practice:${BUILD_NUMBER}
                '''
            }
        }
    }
}


        
        stage('Checkout K8S manifest SCM'){
            steps {
              git branch: 'main', url: 'https://github.com/abhaykohli/ToDoApp.git'
            }
        }
        
        stage('Update K8S manifest & push to Repo'){
            steps {
                script{
                    withCredentials([gitUsernamePassword(credentialsId: 'GIT_USERNAME', gitToolName: 'Default')]){
                        sh '''
						sh 'cd deploy'
                        cat deploy.yaml
                        sed -i '' "s/ReplaceImageTag/${BUILD_NUMBER}/g" deploy.yaml
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
