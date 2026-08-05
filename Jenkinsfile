pipeline {

    agent any

    tools {
        maven 'Maven-3.9.9'
    }

    stages {

        stage('Checkout Code') {
            steps {
                checkout scm
            }
        }


        stage('Build') {
            steps {
                sh '''
                mvn clean package -DskipTests
                '''
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
}