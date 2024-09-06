pipeline {
    agent { label 'Jenkins-Agent' }
    tools {
        jdk 'Java17'
        maven 'Maven3'
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
