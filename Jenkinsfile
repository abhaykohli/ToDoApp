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
        DOCKER_TAG = "abhaykohli/devops_practice:${BUILD_NUMBER}"
        // DOCKERFILE_LOCATION = "java-maven-sonar-argocd-helm-k8s/spring-boot-app/Dockerfile"
        REGISTRY_CREDENTIALS = credentials('docker-creds')
      }
      steps {
        script {
            sh 'docker build -t ${DOCKER_TAG} .'
            def dockerImage = docker.image("${DOCKER_TAG}")
            docker.withRegistry('https://index.docker.io/v1/', "docker-creds") {
                dockerImage.push()
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
			environment {
				GIT_REPO_NAME = "ToDoApp"
				GIT_USER_NAME = "abhaykohli"
			}
				steps {
					script {
							withCredentials([string(credentialsId: 'GITHUB', variable: 'github-pat')]) {
							sh '''
							git config user.name "abhaykohli"
							cd deploy
							git config user.email "abhaykohli01@gmail.com"
							cat deploy.yaml
							sed -i "s/ReplaceImageTag/${BUILD_NUMBER}/g" deploy.yaml
							cat deploy.yaml
							git add deploy.yaml
							git commit -m 'Updated the deploy yaml | Jenkins Pipeline'
							git remote -v
							git push "https://${github-pat}@github.com/${GIT_USER_NAME}/${GIT_REPO_NAME}" HEAD:main
						'''
            }
        }
    }
}

        }
    }
}
