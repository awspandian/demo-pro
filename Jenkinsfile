pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                git branch: 'j16', url: 'https://github.com/awspandian/demo-pro.git'
            }
        }
stage('Build'){
steps {
sh 'mvn clean'
sh 'mvn install'

}
}
        stage ('Build Docker Image') {

            steps {
                script {
                  app = docker.build("dockerpandian/june")
                  app.inside {
                      sh 'echo$(curl localhost:8080)'
                  }  
                    
                }
            }
        }
        stage('Push to DockerHub') {
            steps {
                script{
                    docker.withRegistry('https://registry.hub.docker.com', 'dd'){
                        app.push("${env.BUILD_NUMBER}")
                        app.push("latest")         
                    }
  
                }
            }
        }
        stage('DeployToProduction') {

            steps {
                input 'Deploy to Production?'
                milestone(1)
                withCredentials([usernamePassword(credentialsId: 'webserver', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    script {
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker pull dockerpandian/june:${env.BUILD_NUMBER}\""
                        try {
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker stop azcs\""
                            sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker rm azcs\""
                        } catch (err) {
                            echo: 'caught error: $err'
                        }
                        sh "sshpass -p '$USERPASS' -v ssh -o StrictHostKeyChecking=no $USERNAME@$prod_ip \"docker run --restart always --name azcs -p 8080:8080 -d dockerpandian/june:${env.BUILD_NUMBER}\""
                    }
                }
            }
        }
    }
}	
