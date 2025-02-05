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
        /*
        stage ('Quality gate') {
            steps {
                sleep(5)
                timeout(time: 1, unit: 'MINUTES'){
                    waitForQualityGate abortPipeline: true
                }
            }
        }
        */

        stage ('Deploy Backend') {
            steps {
                deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks-backend', war: 'target/tasks-backend.war'
            }
        }

        stage ('API Test') {
            steps {
                dir('api-test') {
                    git credentialsId: 'GLourenco', url: 'https://github.com/Gustavo-Lourenco/tasks-api-test'
                    bat 'mvn test'
                }
            }
        }

        stage ('Deploy Frontend') {
            steps {
                dir('frontend'){
                    git credentialsId: 'GLourenco', url: 'https://github.com/Gustavo-Lourenco/tasks-frontend'
                    bat 'mvn clean package'
                    deploy adapters: [tomcat8(credentialsId: 'TomcatLogin', path: '', url: 'http://localhost:8001/')], contextPath: 'tasks', war: 'target/tasks.war'
                }
                
            }
        }

        stage ('Functional Test') {
            steps {
                dir('functional-test') {
                    git credentialsId: 'GLourenco', url: 'https://github.com/Gustavo-Lourenco/tasks-functional-tests'
                    bat 'mvn test'
                }
            }
        }

    }
}

