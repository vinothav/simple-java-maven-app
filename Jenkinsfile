pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER_URL = "http://artifactory/artifactory"
        SONAR_URL = "http://sonar:9000"
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

        stage("Build and analyse code") {
            parallel {
                stage ('Exec Maven') {
                    steps {
                        rtMavenRun (
                        pom: 'pom.xml',
                        goals: 'clean compile',
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

                stage("Code Analysis") {
                    steps {
                        withSonarQubeEnv('SonarQubeServer'){
                            withCredentials([string(credentialsId: 'java-project-sonar-key', variable: 'project-key')]) {
                                sh 'mvn sonar:sonar -Dsonar.projectKey=Java-project-1 -Dsonar.host.url=SONAR_URL -Dsonar.login=project-key'
                            }
                        }
                   }
                }
            } 
        } 

        stage("Test the code"){
            steps {
                sh "mvn clean test"
            }

       }
    }
}