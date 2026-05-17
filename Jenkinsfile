pipeline {

    agent any

    environment {

        DOCKERHUB_USERNAME = "kabshataiyub"

        IMAGE_NAME = "knight-bite"

        APP_PORT = "9090"

    }

    stages {

        stage('Clone') {

            steps {

                git 'https://github.com/MohammedTaiyubV/Knight-Bite.git'

            }
        }

        stage('Get Public IP') {

            steps {

                script {

                    env.PUBLIC_IP = sh(
                        script: "curl -s https://api.ipify.org",
                        returnStdout: true
                    ).trim()

                    env.APP_URL = "http://${PUBLIC_IP}:${APP_PORT}"

                }
            }
        }

        stage('Create Env File') {

            steps {

                sh 'echo BUILD_NUMBER=${BUILD_NUMBER} > .env'

            }
        }

        stage('Build & Deploy') {

            steps {

                sh 'docker compose up -d --build'

            }
        }

        stage('Push Docker Image') {

            steps {
            // everytime you need to change this inside script part based on your jenkins docker registry 
                script {

                    withDockerRegistry(
                        credentialsId: 'b181dd43-16a4-4511-a5cd-23abe6a71379'
                    ) {

                        sh "docker push ${DOCKERHUB_USERNAME}/${IMAGE_NAME}:v${BUILD_NUMBER}"

                    }
                }
            }
        }
    }

    post {

        success {

            mail to: 'ashishwayachal14@gmail.com,kabshataiyub@gmail.com',

            subject: "SUCCESS : ${JOB_NAME} #${BUILD_NUMBER}",

            body: """

Build Status : SUCCESS

Job Name : ${JOB_NAME}

Build Number : ${BUILD_NUMBER}

Application URL :
${APP_URL}

Docker Image :
${DOCKERHUB_USERNAME}/${IMAGE_NAME}:v${BUILD_NUMBER}

"""
        }

        failure {

            mail to: 'ashishwayachal14@gmail.com,kabshataiyub@gmail.com',

            subject: "FAILED : ${JOB_NAME} #${BUILD_NUMBER}",

            body: """

Build Status : FAILED

Job Name : ${JOB_NAME}

Build Number : ${BUILD_NUMBER}

Application URL :
${APP_URL}

Please check deployment logs.

"""
        }
    }
}
