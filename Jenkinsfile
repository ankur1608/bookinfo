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
// dockerImage = docker.build registry + ":$BUILD_NUMBER"
sh """
IMAGE="$registry:$BUILD_NUMBER" 
sudo podman build -t \${IMAGE} . 
"""

}}
}
}
stage('Deploy our image') {
steps{
script {
docker.withRegistry( '', registryCredential ) {
// dockerImage.push()
                    sh """
                    #!/bin/bash

                    # Construct Image Name
                    IMAGE="$registry:$BUILD_NUMBER" 

                    // podman login -u ${USERNAME} -p ${PASSWORD} ${REGISTRY} --tls-verify=false

                    podman push \${IMAGE} --tls-verify=false
                    """
}
}
}
}
stage('Cleaning up') {
steps{
sh "docker rmi $registry:$BUILD_NUMBER"
}
}
}
}
