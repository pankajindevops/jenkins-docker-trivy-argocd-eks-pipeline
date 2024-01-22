pipeline {
    // Define the Node Agent in my pipeline
    agent { label 'jenkins-agent' }
    // Define all the Tools - Plugins that have been Configured in Jenkins
    tools {
        jdk 'Java17'
        maven 'Maven3'
    }
    stages {
        stage("Cleanup our Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from Github Source Control") {
            steps {
                git branch: 'main', credentialsId: 'github', url:'https://github.com/pankajindevops/jenkins-docker-trivy-argocd-eks-pipeline'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test the Application") {
            steps {
                sh "mvn test"
            }
        }
    }
}