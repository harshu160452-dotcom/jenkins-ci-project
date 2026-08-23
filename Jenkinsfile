pipeline {

    agent {
        label 'Jenkins-Agent'
    }

    tools {
        jdk 'java21'
        maven 'Maven'
    }

    stages {

        stage('Git Checkout') {
            steps {
                git(
                    branch: 'main',
                    credentialsId: 'github',
                    url: 'https://github.com/harshu160452-dotcom/jenkins-ci-project.git'
                )
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean package'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }
    }
}
