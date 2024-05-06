pipeline {
    agent any
    tools {
        maven "MAVEN"
    }
    stages {
        stage("Clone code from GitHub") {
            steps {
                script {
                    git branch: 'main', credentialsId: 'githubwithpassword', url: 'https://github.com/Hujefas/jenkins-nexus';
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
        stage('upload artifacts') {
            steps {
                nexusArtifactUploader artifacts: [[artifactId: 'my-app', classifier: '', file: '/var/lib/jenkins/workspace/jenkins-nexus/target/my-app-1.0-SNAPSHOT.jar', type: 'jar']], credentialsId: 'NEXUS_CRED', groupId: 'com.mycompany.app', nexusUrl: '3.109.152.68:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-central-repository', version: '1.0-SNAPSHOT'
            }
        }
    }
}
