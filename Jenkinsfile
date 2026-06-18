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
                dependencyCheck additionalArguments: '--scan ./ --format HTML', odcInstallation: 'OWASP-dc'
                #dependencyCheckPublisher pattern: '**/dependency-check-report.xml'
            }
        }
    }
}

