
pipeline {
    agent any

    environment {
        JAVA_HOME = 'C:\\Program Files\\Zulu\\zulu-17'
        MAVEN_HOME = 'C:\\Program Files\\Apachemaven-3.9.12'
        PATH = 'C:\\Program Files\\Zulu\\zulu-17\\bin;C:\\Program Files\\Apachemaven-3.9.12\\bin;C:\\Windows\\System32;C:\\Windows'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    parameters {
        booleanParam(
            name: 'DEPLOY_TO_CLOUDHUB2',
            defaultValue: false,
            description: 'Set to true only when CloudHub 2.0 deployment is configured.'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm

                bat '''
                    @echo off
                    echo ===== GIT STATUS =====
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
                    mvn -version
                '''
            }
        }

        stage('Clean') {
            steps {
                bat '''
                    @echo off
                    call mvn -B -ntp clean
                    if errorlevel 1 exit /b %ERRORLEVEL%
                '''
            }
        }

        stage('Build, Test and Verify') {
            steps {
                bat '''
                    @echo off
                    call mvn -B -ntp verify
                    if errorlevel 1 exit /b %ERRORLEVEL%
                '''
            }
        }

        stage('Archive Mule Application') {
            steps {
                archiveArtifacts(
                    artifacts: 'target/*-mule-application.jar',
                    fingerprint: true
                )
            }
        }

        stage('Deploy to CloudHub 2.0') {
            when {
                expression {
                    return params.DEPLOY_TO_CLOUDHUB2
                }
            }

            steps {
                script {
                    
                    def anypointClientId = '377012aa9ddc4b6dafd9793b98ec51a7'
                    def anypointClientSecret = 'beBac14E4172445C810D2600533442CF'

                    withEnv([
                        "ANYPOINT_CLIENT_ID=${anypointClientId}",
                        "ANYPOINT_CLIENT_SECRET=${anypointClientSecret}"
                    ]) {
                        bat '''
                            @echo off
                            call mvn -B -ntp mule:deploy -DconnectedAppClientId="%ANYPOINT_CLIENT_ID%" -DconnectedAppClientSecret="%ANYPOINT_CLIENT_SECRET%" -DconnectedAppGrantType=client_credentials
                            if errorlevel 1 exit /b %ERRORLEVEL%
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'Pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Review the Jenkins Console Output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}