pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER_URL = "http://artifactory:8081/artifactory"
    }

    tools {
        maven '3.6.3'
    }

    stages {
        stage("Configure Artifactory server") {
            steps {
                rtServer (
                    id: "ARTIFACTORY_SERVER",
                    url: "http://artifactory:8081/artifactory",
                    username: 'admin',
                    password: 'Siva-338;'
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

        stage("Publish the build info"){
            steps {
                rtPublishBuildInfo (
                    serverId: "ARTIFACTORY_SERVER"
                )
            }
        }

        stage("Test the code"){
            steps {
                sh "mvn clean test"
            }
        }

    }

    post {
        always {
            cleanWs()
        }
    }
}