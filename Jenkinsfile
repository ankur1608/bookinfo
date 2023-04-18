pipeline {
environment {
registry = "docker.io/ankur1308/bookinfo_cicd"
registryCredential = 'ankur_dockerhub_cred'
dockerImage = ''
}
agent any
stages {
stage('Build Image') {
steps{
dir('src/productpage') {
script {
sh """
IMAGE="$registry:$BUILD_NUMBER" 
sudo podman build -t \${IMAGE} . 
"""

}}
}
}
stage('SonarQube Analysis') {
def scannerHome = tool 'sonarqube4.8.0'
withSonarQubeEnv('sonar_scanner') {
sh """/var/lib/jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonarqube4.8.0/bin/sonar-scanner -D sonar.projectVersion=1.0-SNAPSHOT -D sonar.login=admin      -D sonar.password=Passw0rd@123 \
        -D sonar.projectBaseDir=/var/lib/jenkins/workspace/bookinfo/ \
        -D sonar.projectKey=bookinfo \
        -D sonar.sourceEncoding=UTF-8 \
        -D sonar.language=python \
        -D sonar.sources=src/productpage \
        -D sonar.host.url=http://jenkin-server.10.25.55.71.nip.io:9000/"""
}
}
stage('Push Image to registry') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
// dockerImage.push()
                    sh """
                    #!/bin/bash

                    # Construct Image Name
                    IMAGE="$registry:$BUILD_NUMBER" 

                    sudo podman push \${IMAGE} --tls-verify=false
                    """
}
}
}
}
stage('App deploy') {
steps{
sh "kubectl --kubeconfig /var/lib/jenkins/k3s_config set image deployment productpage-v1 productpage=$registry:$BUILD_NUMBER"
}
}   
stage('Cleaning up') {
steps{
sh "sudo podman rmi $registry:$BUILD_NUMBER"
}
}
}
}
