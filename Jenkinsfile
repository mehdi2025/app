pipeline {
    agent any
    tools {
        jdk 'jdk17'
        nodejs 'node16'
    }
    environment {
        SCANNER_HOME = tool 'sonar-scanner'
        APP_NAME = "app"
        RELEASE = "1.0.0"
        DOCKER_USER = "mehdichitta"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
	
    }
    stages {
        stage('clean workspace') {
            steps {
                cleanWs()
            }
        }
        stage('Checkout from Git') {
            steps {
                git branch: 'main', url: 'https://github.com/mehdi2025/app.git'
            }
        }
        
       
	 stage("Docker Build & Push"){
             steps{
                 script{
                   withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker'){   
                      sh "docker build -t app ."
                      sh "docker tag app mehdichitta/app:latest "
                      sh "docker push mehdichitta/app:latest "
                    }
                }
            }
        }
       
	
        
    }
    }
