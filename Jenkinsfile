pipeline {

    agent {
        label 'Jenkins-Agent'
    }

    tools {
        jdk 'java21'
        maven 'Maven3'
    }

    environment {

        // Application
        APP_NAME = 'register-app-pipeline'
        RELEASE = '1.0.0'

        // Docker Hub
        DOCKER_USER = 'harshitx12'
        IMAGE_NAME = "${DOCKER_USER}/${APP_NAME}"
        IMAGE_TAG  = "${RELEASE}-${BUILD_NUMBER}"

        // Jenkins Credentials
        DOCKER_CREDENTIALS_ID = 'dockerhub-credentials'
        GITHUB_CREDENTIALS_ID = 'github'
        SONAR_CREDENTIALS_ID  = 'jenkins-sonarqube-token'
    }

    stages {

        /*
         * 1. Clean Workspace
         */
        stage('Cleanup Workspace') {
            steps {
                cleanWs()
            }
        }

        /*
         * 2. Checkout Source Code
         */
        stage('Checkout from SCM') {
            steps {
                git branch: 'main',
                    credentialsId: "${GITHUB_CREDENTIALS_ID}",
                    url: 'https://github.com/harshu160452-dotcom/jenkins-ci-project.git'
            }
        }

        /*
         * 3. Build Application
         */
        stage('Build Application') {
            steps {
                sh 'mvn clean package -DskipTests'
            }
        }

        /*
         * 4. Run Tests
         */
        stage('Test Application') {
            steps {
                sh 'mvn test'
            }
        }

        /*
         * 5. SonarQube Code Analysis
         */
        stage('SonarQube Analysis') {
            steps {

                withSonarQubeEnv(
                    installationName: 'sonarqube-server',
                    credentialsId: "${SONAR_CREDENTIALS_ID}"
                ) {

                    sh 'mvn sonar:sonar'
                }
            }
        }

        /*
         * 6. Wait for SonarQube Quality Gate
         */
        stage('Quality Gate') {
            steps {

                timeout(time: 10, unit: 'MINUTES') {

                    waitForQualityGate(
                        abortPipeline: true,
                        credentialsId: "${SONAR_CREDENTIALS_ID}"
                    )
                }
            }
        }

        /*
         * 7. Build Docker Image
         */
        stage('Build Docker Image') {
            steps {

                sh """
                    docker build \
                    -t ${IMAGE_NAME}:${IMAGE_TAG} \
                    -t ${IMAGE_NAME}:latest \
                    .
                """
            }
        }

        /*
         * 8. Login to Docker Hub
         */
        stage('Docker Hub Login') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: "${DOCKER_CREDENTIALS_ID}",
                        usernameVariable: 'DOCKER_USERNAME',
                        passwordVariable: 'DOCKER_PASSWORD'
                    )
                ]) {

                    sh '''
                        echo "$DOCKER_PASSWORD" | docker login \
                        -u "$DOCKER_USERNAME" \
                        --password-stdin
                    '''
                }
            }
        }

        /*
         * 9. Push Docker Image to Docker Hub
         */
        stage('Push Docker Image') {
            steps {

                sh """
                    docker push ${IMAGE_NAME}:${IMAGE_TAG}
                    docker push ${IMAGE_NAME}:latest
                """
            }
        }
    }

    /*
     * Post Actions
     */
    post {

        success {
            echo "CI Pipeline completed successfully."
            echo "Docker Image: ${IMAGE_NAME}:${IMAGE_TAG}"
        }

        failure {
            echo "CI Pipeline failed. Check the Jenkins console logs."
        }

        always {
            sh 'docker logout || true'
        }
    }
}
stage('Build & Push Docker Image') {
    steps {
        script {

            docker.withRegistry('', DOCKER_PASS) {

                docker_image = docker.build("${IMAGE_NAME}")

            }

            docker.withRegistry('', DOCKER_PASS) {

                docker_image.push("${IMAGE_TAG}")
                docker_image.push('latest')

            }
        }
    }
}
