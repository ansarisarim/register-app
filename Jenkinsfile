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
        JENKINS_API_TOKEN = credentials("JENKINS_API_TOKEN")
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

            stage("Trigger CD Pipeline") {
            steps {
                script {
                    sh "curl -v -k --user sarim:${JENKINS_API_TOKEN} -X POST -H 'cache-control: no-cache' -H 'content-type: application/x-www-form-urlencoded' --data 'IMAGE_TAG=${IMAGE_TAG}' 'ec2-13-229-74-44.ap-southeast-1.compute.amazonaws.com:8080/job/register-app--CD/buildWithParameters?token=gitops-token'"
                }
            }
       }
         
    }
}
