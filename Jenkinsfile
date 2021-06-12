pipeline {
    environment { 

        registry = "devopshint/my-app-1.0" 

        registryCredential = 'devopshint' 

    }
    
    agent any
    tools {
        maven 'MAVEN'
    }

    stages {

        stage('Build Maven') {
            steps{
                checkout([$class: 'GitSCM', branches: [[name: '*/main']], extensions: [], userRemoteConfigs: [[credentialsId: 'GIT_REPO', url: 'https://github.com/devopshint/sonarqube.git']]])

                sh "mvn -Dmaven.test.failure.ignore=true clean package"
                
            }
        }   
    stage("Publish to Nexus Repository Manager") {

            steps {

                script {

                    pom = readMavenPom file: "pom.xml";

                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");

                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"

                    artifactPath = filesByGlob[0].path;

                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {

                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            
                            protocol: 'http',

                            nexusUrl: '18.191.17.124:8081',

                            groupId: 'pom.com.mycompany.app',

                            version: 'pom.1.0-SNAPSHOT',

                            repository: 'maven-nexus-repository',

                            credentialsId: 'NEXUS_CRED',

                            artifacts: [

                                [artifactId: 'pom.my-app',

                                classifier: '',

                                file: artifactPath,

                                type: pom.packaging],

                                [artifactId: 'pom.my-app',

                                classifier: '',

                                file: "pom.xml",

                                type: "pom"]

                            ]

                        );

                    } else {

                        error "*** File: ${artifactPath}, could not be found";

                    }

                }

            }

        }
    stage('SonarQube Analysis') {
    steps{
        script{
    def scannerHome = tool "SonarQube";
      withSonarQubeEnv('SonarQube') {
      sh """/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/SonarQube/bin/sonar-scanner \
     -D sonar.projectVersion=1.0-SNAPSHOT \
       -D sonar.login=admin \
      -D sonar.password=admin \
      -D sonar.projectBaseDir=/var/lib/jenkins/workspace/new-jenkins \
        -D sonar.projectKey=my-app1 \
        -D sonar.sourceEncoding=UTF-8 \
        -D sonar.language=java \
        -D sonar.sources=src/main \
        -D sonar.tests=src/test \
        -D sonar.host.url=http://18.191.154.53:9000/"""
        }
    }
}
}
  stage('Build Docker Image') {
            steps {
                script {
                  sh 'docker build -t devopshint/my-app-1.0:latest .'
                }
            }
        }   
    stage('Deploy Image') {
        steps {
            script {
                docker.withRegistry( '', registryCredential ) {
                 sh 'docker push devopshint/my-app-1.0:latest'

                }
    }
            }
    }
    }
}