pipeline {
    agent any

    tools {
        maven 'Maven3'
    }

    environment {
        PATH = "C:\\Program Files\\Docker\\Docker\\resources\\bin;${env.PATH}"
        JAVA_HOME = 'C:\\Program Files\\Java\\jdk-17'
        SONARQUBE_SERVER = 'SonarQubeServer'
        SONAR_TOKEN = 'sqa_ad527e43d83ca16dd71a77a3b1df489f513635ef'
        DOCKERHUB_CREDENTIALS_ID = 'Docker_Hub'
        DOCKERHUB_REPO = 'amirdirin/sep2_week5_f2025'
        DOCKER_IMAGE_TAG = 'latest'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/Toiriku/sonarjenkins.git'
            }
        }

        stage('Build') {
            steps {
                bat 'mvn clean install'
            }
        }

        stage('SonarQube Analysis') {
                steps {
                    dir('sep2_week5_inclass_s2') {
                        withSonarQubeEnv('SonarQubeServer') {
                            bat """
                                mvn sonar:sonar ^
                                -Dsonar.projectKey=devops-demo ^
                                -Dsonar.projectName=DevOps-Demo ^
                                -Dsonar.host.url=http://localhost:9000 ^
                                -Dsonar.login=${env.SONAR_TOKEN} ^
                                -Dsonar.coverage.jacoco.xmlReportPaths=target/site/jacoco/jacoco.xml
                            """
                        }
                    }
                }
            }

        stage('Build Docker Image') {
            steps {
                script {
                    docker.build("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}")
                }
            }
        }

        stage('Push Docker Image to Docker Hub') {
            steps {
                script {
                    docker.withRegistry('https://index.docker.io/v1/', DOCKERHUB_CREDENTIALS_ID) {
                        docker.image("${DOCKERHUB_REPO}:${DOCKER_IMAGE_TAG}").push()
                    }
                }
            }
        }
    }
}
