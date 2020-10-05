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
        stage('sonarQube') {
            steps {
                sh "mvn sonar:sonar"
            }
        }
        stage('Code Quality') {
           steps {
               sh "mvn checkstyle:checkstyle pmd:pmd pmd:cpd findbugs:findbugs"
           }
           post {
               always {
                   recordIssues enabledForFailure: true, tools: [mavenConsole(), java(), javaDoc()]
                   recordIssues enabledForFailure: true, tool: checkStyle()
                   recordIssues enabledForFailure: true, tool: cpd(pattern: '**/target/cpd.xml')
                   recordIssues enabledForFailure: true, tool: pmdParser(pattern: '**/target/pmd.xml')
               }
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
                    pom: 'pom.xml',
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
    }
}
                   

