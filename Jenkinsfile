
pipeline {
    agent any

    tools {
        jdk 'jdk17'
        maven 'maven'
    }

    options {
        timestamps()
        disableConcurrentBuilds()
    }

    parameters {
        booleanParam(
            name: 'DEPLOY_TO_CLOUDHUB2',
            defaultValue: false,
            description: 'Enable when CloudHub 2.0 deployment is configured.'
        )
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
                sh 'git status --short'
            }
        }

        stage('Clean') {
            steps {
                sh 'mvn -B -ntp clean'
            }
        }

        stage('Build, Test, Package and Verify') {
            steps {
                sh 'mvn -B -ntp verify'
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
                    params.DEPLOY_TO_CLOUDHUB2
                }
            }

            steps {
                script {
                    // DEMO VALUES ONLY
                    // Replace locally for demonstration.
                    def anypointClientId = '377012aa9ddc4b6dafd9793b98ec51a7'
                    def anypointClientSecret = 'beBac14E4172445C810D2600533442CF'

                    withEnv([
                        "ANYPOINT_CLIENT_ID=${anypointClientId}",
                        "ANYPOINT_CLIENT_SECRET=${anypointClientSecret}"
                    ]) {
                        sh '''
                            set +x

                            mvn -B -ntp mule:deploy \
                              -DconnectedAppClientId="$ANYPOINT_CLIENT_ID" \
                              -DconnectedAppClientSecret="$ANYPOINT_CLIENT_SECRET" \
                              -DconnectedAppGrantType=client_credentials
                        '''
                    }
                }
            }
        }
    }

    post {
        success {
            echo 'CI pipeline completed successfully.'
        }

        failure {
            echo 'Pipeline failed. Check the Jenkins console output.'
        }

        always {
            echo 'Pipeline execution finished.'
        }
    }
}