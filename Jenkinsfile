pipeline {
    agent { label 'Jenkins-Agent' }

    tools {
        jdk 'java21'
        maven 'Maven'
    }

    stages {
        stage('Git Checkout') {
            steps {
                git branch: 'main',
                    credentialsId: 'git-credentials',
                    url: 'YOUR_GITHUB_REPO_URL'
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
