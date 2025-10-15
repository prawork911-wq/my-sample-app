pipeline {
    agent any
        tools {
        maven 'Maven-3.9'
        jdk 'jdk17'
    }

    environment {
        NEXUS_CREDENTIALS = credentials('nexus-credentials')
        SONARQUBE_ENV = 'sonarqube'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/prawork911-wq/my-sample-app.git'
        }

        stage('Code Quality - SonarQube') {
            steps {
                withSonarQubeEnv('sonarqube') {
                    sh 'mvn clean verify sonar:sonar'
                }
            }
        }

        stage('Build & Package') {
            steps {
                sh 'mvn clean package -DskipTests=true'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                sh """
                  mvn deploy \
                  -DskipTests \
                  -Dnexus.username=${NEXUS_CREDENTIALS_USR} \
                  -Dnexus.password=${NEXUS_CREDENTIALS_PSW}
                """
            }
        }
    }

    post {
        success {
            echo "✅ Build and Deployment to Nexus Successful!"
        }
        failure {
            echo "❌ Build Failed. Check Console Output."
        }
    }
}
