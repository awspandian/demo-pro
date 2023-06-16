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
            when {
                branch 'j16'
            }
            steps {
                script {
                  app = docker.build("dockerpandian/june")
                  app.inside {
                      sh 'echo$(curl localhost:8080)'
                  }  
                    
                }
            }
        }
}
}
