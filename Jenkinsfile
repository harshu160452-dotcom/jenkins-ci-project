pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'java21'
        maven 'Maven3'
    }

    stages {
        stage("Cleanup Workspace") {
            steps {
                cleanWs()
            }
        }

        stage("Checkout from SCM") {
            steps {
               git branch: 'main',
    credentialsId: 'github',
    url: 'https://github.com/harshu160452-dotcom/jenkins-ci-project.git'
            }
        }

        stage("Build Application") {
            steps {
                sh "mvn clean package"
            }
        }

        stage("Test Application") {
            steps {
                sh "mvn test"
            }
        }

        stage("SonarQube Analysis") {
            steps {
                script {
                    withSonarQubeEnv(credentialsId: 'jenkins-sonarqube-token1') {
                        sh "mvn sonar:sonar"
                    }
                }
            }
        }
    }
}
