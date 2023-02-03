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
        withCredentials([string(credentialsId: 'docker-hub-pass', variable: 'docker_pass')]) {
            sh "docker login -u rajesh1218 -p ${docker_pass}"
            sh "docker push rajesh1218/$JOB_NAME:v1.$BUILD_ID"
            sh "docker push rajesh1218/$JOB_NAME:v1.latest"

            sh "docker rmi $JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.$BUILD_ID rajesh1218/$JOB_NAME:v1.latest"
        }
    }
    stage("SSH Into k8s Server") {
        def remote = [:]
        remote.name = 'K8S master'
        remote.host = '192.168.56.111'
        remote.user = 'vagrant'
        remote.password = 'vagrant'
        remote.allowAnyHosts = true
        
        stage('Put deployment.yml& service.yml onto k8smaster') {
            sshPut remote: remote, from: 'deploy.yml', into: '.'
            sshPut remote: remote, from: 'service.yml', into: '.'
        }
        stage('Deploy spring boot to k8s') {
            sshCommand remote: remote, command: "kubectl apply -f deploy.yml"
            sshCommand remote: remote, command: "kubectl apply -f service.yml"  
        }
    }
}