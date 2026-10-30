pipeline {
    agent any

    environment {
        NEXUS_URL = 'http://localhost:8081'
        NEXUS_REPO = 'lab-raw-repo'
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build Artifact') {
            steps {
                bat '''
                    if not exist build mkdir build
                    powershell Compress-Archive -Path src/* -DestinationPath build/sample-app-%BUILD_NUMBER%.zip -Force
                '''
            }
        }

        stage('Upload to Nexus') {
            steps {
                withCredentials([
                    usernamePassword(
                        credentialsId: 'nexus-credentials',
                        usernameVariable: 'NEXUS_USER',
                        passwordVariable: 'NEXUS_PASS'
                    )
                ]) {
                    bat '''
                        curl --fail -v -u %NEXUS_USER%:%NEXUS_PASS% --upload-file build/sample-app-%BUILD_NUMBER%.zip %NEXUS_URL%/repository/%NEXUS_REPO%/sample-app/sample-app-%BUILD_NUMBER%.zip
                    '''
                }
            }
        }
    }
}
