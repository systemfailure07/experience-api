
pipeline {
    agent any

    environment {
        JAVA_HOME = 'C:\\Program Files\\Zulu\\zulu-17'
        MAVEN_HOME = 'C:\\Program Files\\Apachemaven-3.9.12'

        PATH = "C:\\Program Files\\Zulu\\zulu-17\\bin;C:\\Program Files\\Apachemaven-3.9.12\\bin;C:\\Program Files\\Git\\cmd;${env.PATH}"
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    stages {

        stage('Check Git') {
            steps {
                bat '''
                    @echo off
                    git --version
                    git status --short
                '''
            }
        }

        stage('Check Java and Maven') {
            steps {
                bat '''
                    @echo off
                    echo ===== JAVA VERSION =====
                    java -version

                    echo ===== MAVEN VERSION =====
                    call mvn -version
                '''
            }
        }

        stage('Maven Clean') {
            steps {
                bat 'call mvn clean'
            }
        }

        stage('Maven Package') {
            steps {
                bat 'call mvn package'
            }
        }

        stage('Maven Verify') {
            steps {
                bat 'call mvn verify'
            }
        }

        stage('Maven Test') {
            steps {
                bat 'call mvn test'
            }
        }

        stage('Maven Deploy') {
            steps {
                bat 'call mvn deploy -s "C:\\Users\\10361722\\.m2\\settings.xml"'
            }
        }
    }

    post {
        success {
            echo 'SUCCESS: All five Maven commands completed successfully.'
        }

        failure {
            echo 'FAILURE: Check Console Output for the failed stage.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}