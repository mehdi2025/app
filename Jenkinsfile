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
	JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
	
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
        
       
	 stage("Docker Build & Push") {
    steps {
        script {
            withDockerRegistry(credentialsId: 'dockerhub', toolName: 'docker') {   
                sh "docker build -t ${IMAGE_NAME}:${IMAGE_TAG} ."
                sh "docker push ${IMAGE_NAME}:${IMAGE_TAG}"
                
                // Optional: still push latest for convenience
                sh "docker tag ${IMAGE_NAME}:${IMAGE_TAG} ${IMAGE_NAME}:latest"
                sh "docker push ${IMAGE_NAME}:latest"
            }
        }
    }
}
          stage ('Cleanup Artifacts') {
             steps {
                 script {
                      sh "docker rmi ${IMAGE_NAME}:${IMAGE_TAG}"
                      sh "docker rmi ${IMAGE_NAME}:latest"
                 }
             }
         }
	 stage('Trigger CD Pipeline') {
    steps {
        script {
            def response = httpRequest(
                acceptType: 'APPLICATION_JSON',
                contentType: 'APPLICATION_JSON',
                httpMode: 'POST',
                url: 'http://192.168.1.3:8080/job/app-CD/buildWithParameters?token=gitops-token&IMAGE_TAG=' + "${IMAGE_TAG}",
                validResponseCodes: '200:201'
            )
            echo "CD Pipeline triggered: ${response.status}"
        }
    }
}
       
	
        
    }
    }
