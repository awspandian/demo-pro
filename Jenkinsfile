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
		stage ('Deploy to production') {
		steps{
		input 'Deploy to production?'
			milestone(1)
			withCredentials([usernamePassword(credentialsID: 'webserver' , usernameVariable: 'USERNAME' , passwordVariable: 'USERPASS') ]) {
			script {
			  sh "sshpass -p $USERPASS' -v ssh -o StrictHostkeyChecking=no $USERNAME@$prod_ip \ "docker pull dockerpandian/june:${env.BUILD_NUMBER}\""
			  try {
			    sh "sshpass -p $USERPASS' -v ssh -o StrictHostkeyChecking=no $USERNAME@$prod_ip \ "docker stop hippo\""
				sh "sshpass -p $USERPASS' -v ssh -o StrictHostkeyChecking=no $USERNAME@$prod_ip \ "docker rm hippo\""
				} catch (err){
				echo: 'caught error: $err'
							}
			sh "sshpass -p $USERPASS' -v ssh -o StrictHostkeyChecking=no $USERNAME@$prod_ip \ " docker ru --restart always --name hippo -p 8080:8080 -d dockerpandian/june:${env.BUILD_NAME}\"" 
			}
			
			}
		}
		}
}
}
