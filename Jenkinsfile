pipeline{
    agent any
        environment{
        PATH ="/usr/bin:$PATH"
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
    }
}
