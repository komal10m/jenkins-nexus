pipeline {
    agent any
    tools {
        maven "maven3"
    }
    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    git branch: 'main', credentialsId: 'githubwithpassword', url: 'https://github.com/komal10m/jenkins-nexus';
                }
            }
        }
        stage("Maven Build") {
            steps {
                script {
                    sh "mvn package -DskipTests=true"
                }
            }
        }
        stage( "upload artifact" ) {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'my-app', classifier: '', file: '/var/lib/jenkins/workspace/jenkins-nexus/target/my-app-1.0-SNAPSHOT.jar', type: 'jar']], credentialsId: 'NEXUS_CRED', groupId: 'com.mycompany.app', nexusUrl: '54.165.249.61:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'demorepository', version: '1.0-SNAPSHOT'
    }
}
