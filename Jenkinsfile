pipeline {
    agent any

    environment {
        ARTIFACTORY_SERVER_URL = "http://172.19.0.5/artifactory"
        SONAR_URL = "http://sonar:9000"
        PROJECT_KEY = credentials('java-project-sonar-key')
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

        stage("Parallel run of the build and analysis") {
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

                stage("Code Analysis") {
                    steps {
                        withSonarQubeEnv('SonarQubeServer'){
                            sh 'mvn sonar:sonar -Dsonar.projectKey=Java-project-1 -Dsonar.host.url=${SONAR_URL} -Dsonar.login=${PROJECT_KEY}'
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

        stage("Publish the build info"){
            steps {
                rtPublishBuildInfo (
                serverId: "ARTIFACTORY_SERVER"
                )
            }
        }
    }
}