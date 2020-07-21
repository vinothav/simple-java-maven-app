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

        stage("Build and analyse code") {
            parallel {
                stage("Build the code") {
                    steps {
                        sh "mvn -version"
                        sh "mvn clean compile"
            }
        }

                stage("Code Analysis") {
                    steps {
                        withSonarQubeEnv('SonarQubeServer'){
                            sh 'mvn sonar:sonar -Dsonar.projectKey=Java-project-1 -Dsonar.host.url=http://sonar:9000 -Dsonar.login=ec18c57ea6955ff092e82e978f7db757bcf01733'
                            }
                   }
  
               }
            } 
        } 

        // stage("Publish the build info"){
        //     steps {
        //         rtPublishBuildInfo (
        //             serverId: "ARTIFACTORY_SERVER"
        //         )
        //     }
        // }

        stage("Test the code"){
            steps {
                sh "mvn clean test"
            }

       }
    }
}