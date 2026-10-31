pipeline {
    agent {
        node {
            label 'AGENT-1'
        }
    }
    environment {
         SUB = "jenkins"
         appVersion = ""
         acc_id = "182312754465"
         PROJECT = "roboshop"
         COMPONENT = "catalogue"
    }
    options {
        timeout(time: 10 , unit: 'MINUTES')
        disableConcurrentBuilds()
    }
    
 // This is Build section
    stages {
        stage('Read Version') {
           steps {
                script{
                    def packageJSON =readJSON file: 'package.json'
                    appVersion = packageJSON.version
                    echo "app version: ${appVersion}"
                
                }                            
           }
        }
        stage('Install Dependecies') {
            steps {
                script{
                   sh  """
                        npm install
                   """
                }               
            }
        }
        stage('Unit Test'){
            steps {
                script{
                    sh """
                        npm test
                    """
                }
            }
        }
        stage('Build Image') {
            steps {
                script{
                   withAWS(region:'us-east-1' , credentials: 'aws-creds'){
                    sh """
                     aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin ${acc_id}.dkr.ecr.us-east-1.amazonaws.com  
                     docker build -t ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion} .
                     docker images
                     docker push   ${acc_id}.dkr.ecr.us-east-1.amazonaws.com/${PROJECT}/${COMPONENT}:${appVersion}
                    """
                   }
                }               
            }
        }
        
    post{
        always{
            echo 'I will always say Hello again'
            cleanWs()
        }
        success{
            echo 'I will run if success'
        }
        failure{
             echo 'I will run if failure'
        }
        aborted {
            echo "pipeline is aborted"
        }
    }
}