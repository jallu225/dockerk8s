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
    stage('Static Code Analysis'){
        withSonarQubeEnv(credentialsId: 'sonar-token') {
            sh 'mvn clean package sonar:sonar'
         }
    }
    stage('Quality Gate Status'){
        waitForQualityGate abortPipeline: false, credentialsId: 'sonar-token'
    }
}