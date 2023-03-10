pipeline {
    agent any
    tools {
            maven "M2_HOME"
    } 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
    }
}