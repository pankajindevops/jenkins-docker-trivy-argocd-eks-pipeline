pipeline {
    // Define the Node Agent in my pipeline
    agent { label 'jenkins-agent' }
    // Define all the Tools - Plugins that have been Configured in Jenkins
    tools {
        jdk 'Java17'
        maven 'Maven3'
        dockerTool 'docker'
    }

    environment {
        APP_NAME = "jenkins-docker-trivy-argocd-eks-pipeline"
        RELEASE = "1.0.0"
        DOCKER_USER = "pankajdocker0710"
        DOCKER_PASS = "dockerhub"
        IMAGE_NAME = "${DOCKER_USER}" + "/" + "${APP_NAME}"
        IMAGE_TAG = "${RELEASE}-${BUILD_NUMBER}"
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

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }

        stage("Quality Gate") {
            steps {
                script {
                    waitForQualityGate abortPipeline: false, credentialsId: 'jenkins-sonarqube-token'
                
                }
            }
        }

        stage("Build & Push Docker Image") {
            steps {
                script {
                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image = docker.build "${IMAGE_NAME}"
                    }

                    docker.withRegistry('',DOCKER_PASS) {
                        docker_image.push("${IMAGE_TAG}")
                        docker_image.push('latest')
                    }                    
                
                }
            }
        }

    }
}