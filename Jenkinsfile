pipeline {
    agent any

    tools {
        maven 'Maven'
        jdk 'JDK17'
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
        }

        stage('SonarQube Analysis') {
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
        // Use settings.xml to provide credentials securely
                sh 'mvn deploy -DskipTests -s settings.xml'
           }
        }

    post {
        success {
            echo '✅ Build & Deploy Successful'
        }
        failure {
            echo '❌ Build Failed'
        }
    }
} // closes pipeline
