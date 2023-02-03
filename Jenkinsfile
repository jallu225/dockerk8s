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
}