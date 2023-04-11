pipeline {
environment {
registry = "ankur1308/bookinfo_cicd"
registryCredential = 'ankur_reg_cred'
dockerImage = ''
}
agent any
stages {
stage('Building our image') {
steps{
dir('src/productpage') {
script {
// dockerImage = sudo podman.build registry + ":$BUILD_NUMBER"
sh 'podman.build registry + ":$BUILD_NUMBER"' 
}}
}
}
stage('Deploy our image') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
dockerImage.push()
}
}
}
}
stage('Cleaning up') {
steps{
sh "podman rmi $registry:$BUILD_NUMBER"
}
}
}
}
