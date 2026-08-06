@Library('jenkins-shared-library') _

pipeline {

    agent any

    tools {
        jdk 'JDK21'
        maven 'Maven-3.9.9'
    }

    options {
        timestamps()
        disableConcurrentBuilds()

        buildDiscarder(logRotator(
            numToKeepStr: '20',
            artifactNumToKeepStr: '10'
        ))

        timeout(time: 30, unit: 'MINUTES')
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkoutCode()
            }
        }

        stage('Display Git Information') {
            steps {
                displayGitInfo()
            }
        }

        stage('Build Application') {
            steps {
                buildMaven()
            }
        }

        stage('Verify Artifact') {
            steps {
                sh '''
                    ls -lh target
                '''
            }
        }

    }

    post {

        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true
        }

        success {
            echo "Build completed successfully."
        }

        failure {
            echo "Build failed."
        }
    }
}