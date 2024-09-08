pipeline {
    agent { label 'jenkins-agent' }
    tools {
        jdk 'java17'
        maven 'maven3'
    }
    environment {
        APP_NAME = "register-app-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "ansarisarim"
        DOCKER_PASS = 'dockerhub'
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
    }

    stages {
        stage("Check out from SCM") {
            steps {
                git branch: 'main', credentialsId: 'github', url: 'https://github.com/ansarisarim/register-app.git'
            }
        }

        stage("build application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("test application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis"){
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins_sonarQube-token') { 
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry("https://index.docker.io/v1/", DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry("https://index.docker.io/v1/", DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }
                }
            }
        }
         stage("Trivy Scan") {
           steps {
               script {
	            sh ('docker run -v /var/run/docker.sock:/var/run/docker.sock aquasec/trivy image ansarisarim/register-app-pipeline:latest --no-progress --scanners vuln  --exit-code 0 --severity HIGH,CRITICAL --format table')
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
        
    }
}
