pipeline {
    agent any
    tools {
        maven "M2_HOME"
    }
    stages {
        stage('Build') {
            steps {
                git 'https://github.com/Valiveti-Neelima/java-project.git'
                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
            }
            post {
                success {
                    junit '**/target/surefire-reports/TEST-*.xml'
                    archiveArtifacts 'target/*.jar'
                }
            }
        }
       stage('Test') {
           steps {
               sh "mvn test"
           }
       }
       stage('Code Quality') {
           steps {
               echo 'Code Quality'
           }
       }
        stage ('Artifactory configuration') {
            steps {
               

                rtMavenDeployer (
                    id: "MAVEN_DEPLOYER",
                    serverId: "artifactory-server",
                    releaseRepo: "libs-release-local",
                    snapshotRepo: "libs-snapshot-local"
                )

                rtMavenResolver (
                    id: "MAVEN_RESOLVER",
                    serverId: "artifactory-server",
                    releaseRepo: "libs-release",
                    snapshotRepo: "libs-snapshot"
                )
            }
        }

        stage ('Exec Maven') {
            steps {
                rtMavenRun (
                    tool: M2_HOME, // Tool name from Jenkins configuration
                    pom: 'java-project/pom.xml',
                    goals: 'clean install package',
                    deployerId: "MAVEN_DEPLOYER",
                    resolverId: "MAVEN_RESOLVER"
                )
            }
        }

        stage ('Publish build info') {
            steps {
                rtPublishBuildInfo (
                    serverId: "artifactory-server"
                )
            }
        }
       stage('Upload Artifacts') {
           steps {
               echo 'Upload Artifacts'
           }
       }
       stage('Deploy') {
           steps {
               echo 'Deploy'
           }
       }
    }
}
