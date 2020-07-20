pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER_URL = "http://artifactory/artifactory"
    }

    tools {
        maven '3.6.3'
    }

    stages {
        stage("Configure Artifactory server") {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: ARTIFACTORY_SERVER_URL,
                    credentialsId: 'Artifactory_creds'
                )
            }
        }

        stage("Build the code"){
            steps {
                sh "mvn -version"
                sh "mvn clean compile"
            }
        }

        stage("Test the code"){
            steps {
                sh "mvn clean test"
            }

            post {
                success {
                    rtUpload (
                        serverId: "ARTIFACTORY_SERVER"
                    )
                }
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}