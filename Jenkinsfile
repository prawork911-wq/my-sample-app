pipeline {
    agent any
    tools {
        // Configure this Maven installation in Jenkins:
        // Manage Jenkins → Global Tool Configuration → Add Maven with Name = "M3"
        maven 'M3' // ensure this name matches the Maven installation you create in Jenkins
    }

    environment {
        // SonarQube server installation name in Jenkins (match configured Name, case-sensitive)
        SONARQUBE = 'SONARQUBE' // change to the exact Name you configured in Jenkins if different
        // Nexus credentials ID stored in Jenkins
        NEXUS_CREDENTIALS = 'nexus-creds'
        // Maven settings.xml location if using Jenkins Managed File
        MAVEN_SETTINGS = 'settings.xml'
    }

    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', url: 'https://github.com/prawork911-wq/my-sample-app.git'
            }
        }

        stage('SonarQube Analysis') {
            steps {
                withSonarQubeEnv("${SONARQUBE}") {
                    sh 'mvn clean verify sonar:sonar -DskipTests'
                }
            }
        }

        stage('Build & Package') {
            steps {
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('Deploy to Nexus') {
            steps {
                withCredentials([usernamePassword(
                    credentialsId: "${NEXUS_CREDENTIALS}",
                    usernameVariable: 'NEXUS_USER',
                    passwordVariable: 'NEXUS_PASSWORD'
                )]) {
                    sh '''
                        mvn deploy -s ${MAVEN_SETTINGS} \
                        -Dnexus.username=$NEXUS_USER \
                        -Dnexus.password=$NEXUS_PASSWORD \
                        -DskipTests
                    '''
                }
            }
        }
    }

    post {
        success {
            echo '✅ Build, SonarQube, and Nexus deploy succeeded!'
        }
        failure {
            echo '❌ Build or deploy failed!'
        }
        always {
            cleanWs()
        }
    }
}