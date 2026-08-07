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

     environment {

        IMAGE_NAME =
        "nikitaharesh/expense-management-system"

        IMAGE_TAG =
        "${BUILD_NUMBER}"

        LATEST_TAG = "latest"


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

                stage('Docker Build') {

            steps {

                dockerBuild(
                    IMAGE_NAME,
                    IMAGE_TAG
                )

            }

        }

         stage('Docker Login') {

            steps {

                dockerLogin(
                    "dockerhub-user",
                    "docker-password"
                )

            }

        }

             stage('Docker Push') {

            steps {

                dockerPush(
                    IMAGE_NAME,
                    IMAGE_TAG
                )

            }

        }



          stage('Deploy to Kubernetes') {
              steps {
                  deployKubernetes(
                      IMAGE_NAME,
                      IMAGE_TAG
                  )
              }
            }

            stage('Cleanup') {
            steps {
                sh '''
                docker image prune -f
                '''
               }
            }



    }

    post {

        always {
            archiveArtifacts artifacts: 'target/*.jar', fingerprint: true

               sh '''
                kubectl get pods -n expense-dev || true
                kubectl get svc -n expense-dev || true
                '''
        }

        success {
            echo "Build completed successfully."
        }

        failure {
            echo "Build failed."
        }
    }
}