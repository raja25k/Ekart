pipeline {
    agent any
    tools{
        maven 'maven-360'
    }
    stages {
        stage('Github Checkout') {
            steps {
                git branch: 'main', changelog: false, credentialsId: 'github-token', poll: false, url: 'https://github.com/raja25k/Ekart.git'
            }
        }
        stage('Compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        stage('OWASP-DC') {
            steps {
                catchError(buildResult: 'SUCCESS', stageResult: 'UNSTABLE') {
                    dependencyCheck(
                        odcInstallation: 'OWASP-dc',
                        nvdCredentialsId: 'NVD-API-KEY',
                        additionalArguments: '--scan .'
                    )
                    dependencyCheckPublisher(
                        pattern: '**/dependency-check-report.xml'
                    )
                }
            }
        }
    }
}

