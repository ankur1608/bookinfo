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
stage('SonarQube analysis') {
steps{
dir('src/productpage') {
def scannerHome = tool 'sonarqube4.8.0'
withSonarQubeEnv('sonar_scanner') {
sh "${scannerHome}/bin/sonar-scanner"
}
}
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
