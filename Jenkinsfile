pipeline {
    agent any
    tools {
        maven "MAVEN_LOCAL"
    } 
    stages {
        stage ('Build Backend') {
            steps {
                bat 'mvn clean package -DskipTests=true'
            }
        }
        stage ('Unit Tests') {
            steps {
                bat 'mvn test'
            }
        }
        stage ('Sonar Analysis') {
            environment{
                scannerHome = tool 'SONAL_SCANNER'
            }
            steps {
                withSonarQubeEnv('SONAR_LOCAL'){
                    bat "${scannerHome}/bin/sonar-scanner -e -Dsonar.projectKey=DeployBack -Dsonar.host.url=http://localhost:9000 -Dsonar.login=5d84c1b01796d1af977d11ac1cc56028b5a69aeb -Dsonar.java.binaries=target -Dsonar.coverage.exclusions=**/.mvn/**,**/src/test/**.**/model/**,**Application.java"
                }
                
            }
        }
        stage ('Quality gate') {
            steps {
                timeout(time: 1, unit: 'MINUTES')
                waitForQualityGate abortPipeline: true
            }
        }
    }
}

