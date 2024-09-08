pipeline {
    agent { label 'jenkins-agent' }
    tools {
        jdk 'java17'
        maven 'maven3'
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
        stage("Sonarqube Analysis") {
            steps {
                 script {
                     withSonerQubeEnv(credentialId: 'jenkins-sonarqube-token') {
                         sh "mvn sonar:sonar"
            }
        }
      }
   }
        
  }
}
