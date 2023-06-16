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
}
}
