pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER_URL = "http://localhost:80"
    }

    tools {
        maven "3.6.0"
    }

    stages {
        stage("Configure Artifactory server") {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: ARTIFACTORY_SERVER_URL,
                    credentialsId: Artifactory_creds
                )
            }
        }

        stage("Build the code"){
            steps {
                sh "mvn -version"
                sh "mvn clean install"
            }
        }
    }

    post {
        always {
            cleanWs()
        }
    }
}