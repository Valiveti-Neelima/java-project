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
    }
}
