pipeline {
    agent { label 'jenkins-agent' }
    tools {
        jdk 'JDK 17'
        maven 'maven3'
    }
    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

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



      
        // Add more stages as needed
    }
}
