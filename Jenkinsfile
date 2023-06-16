pipeline {
    agent any

    stages {
        stage('SCM') {
            steps {
                git branch: 'm03', url: 'https://github.com/awspandian/demo-pro.git'
            }
        }
stage('Build'){
steps {
bat 'mvn clean'
bat 'mvn install'

}
}
}

