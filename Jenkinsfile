pipeline{
    agent any
       tools {
      maven 'M2_home'
    }
    environment {
      DOCKER_TAG = getVersion()
    }
    stages{
        stage("Git checkout"){
            steps{
                git 'https://github.com/likithreddysiddala/sample-project.git'
            }
        }
        stage("Maven build"){
            steps{
                sh "mvn clean install"
			
            }
        }
         stage("Docker Build"){
            steps{
                sh 'docker build . -t likithreddysiddala/app:${DOCKER_TAG} '
            }
        }
         stage("Push DockerHub"){
            steps{
                withCredentials([string(credentialsId: 'docker-hub', variable: 'DockerHubPwd')]) {
                sh 'docker login -u likithreddysiddala -p ${DockerHubPwd}'
                }
               
                sh 'docker push likithreddysiddala/app:${DOCKER_TAG} '
            }
        }
         stage('Docker Deploy'){
            steps{
              ansiblePlaybook credentialsId: '13.209.65.56', disableHostKeyChecking: true, extras: "-e DOCKER_TAG=${DOCKER_TAG}", installation: 'ansible', inventory: 'dev.inv', playbook: 'deploy-docker.yml'
            }
        }
        stage('Deploy to k8s'){
            steps{
                sh "chmod +x changeTag.sh"
                sh "./changeTag.sh ${DOCKER_TAG}"
                sshagent(['43.200.183.232']) {
                    sh "scp -o StrictHostKeyChecking=no services.yml node-app-pod.yml ec2-user@172.31.39.60:/home/ec2-user "
                    script{
                        try{
                            sh "ssh ec2-user@172.31.39.60 kubectl apply -f ."
                        }catch(error){
                            sh "ssh ec2-user@172.31.39.60 kubectl create -f ."
                        }
                    } 
                }
            }
        }
    }
}
def getVersion(){
    def commitHash = sh label: '', returnStdout: true, script: 'git rev-parse --short HEAD'
    return commitHash
}
