node {
    stage ('Git Checkout'){
        git 'https://github.com/jallu225/dockerk8s.git'
    }
    stage('Unit Testing'){
        sh 'mvn test'
    }
    stage('Integration Test'){
        sh 'mvn verify -DskipUnitTests'
    }
    stage('Maven Build'){
        sh 'mvn clean install'
    }
    stage('upload artifacts to nexus'){
        nexusArtifactUploader artifacts: [[artifactId: 'MavenWeb', classifier: '', file: 'target/MavenWeb.war', type: 'war']], credentialsId: 'nexus-cred', groupId: 'com.example', nexusUrl: '192.168.56.112:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'maven-web', version: '1.0.0'
    }
    stage('docker build'){
        sh "docker image build -t $JOB_NAME:v1.$BUILD_ID ."
        sh "docker tag $JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.$BUILD_ID"
        sh "docker tag $JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.latest"
    }
    stage('psuh to docker hub'){
        withCredentials([string(credentialsId: 'DOCKER_HUB_PASSWORD', variable: 'docker-cred')]) {
           sh "docker login -u rajesh1218 -p ${dokcer_cred}"
           sh "docker push rajesh1218/$JOB_NAME:v1.$BUILD_ID"
           sh "docker push rajesh1218/$JOB_NAME:v1.latest"

           sh "docker rmi $JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.latest"
       }
    }
}