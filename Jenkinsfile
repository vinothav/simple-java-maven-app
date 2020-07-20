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

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    pom: 'pom.xml',
                    goals: 'clean install sonar:sonar',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
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
}