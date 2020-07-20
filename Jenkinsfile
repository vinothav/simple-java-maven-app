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

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )   

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "ARTIFACTORY_SERVER",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
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