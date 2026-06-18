pipeline {
    agent any
    options {
        ansiColor('xterm')
    }
    tools{
        maven 'maven-360'
    }
    environment {
        SONAR_SCANNER = tool 'SonarScanner'
    }
    stages {
        stage('Checkout') {
            steps {
                git (
                    branch: 'main', 
                    credentialsId: 'github-token',
                    url: 'https://github.com/raja25k/Ekart.git'
                )
            }
        }
        stage('Compile') {
            steps {
                sh "mvn clean compile -DskipTests=true"
            }
        }
        /* stage('OWASP-DC') {
            steps {
                catchError(
                    buildResult: 'SUCCESS', 
                    stageResult: 'UNSTABLE'
                ) {
                   dependencyCheck(
                        odcInstallation: 'OWASP-dc',
                        //debug: true,
                        //nvdCredentialsId: 'NVD-API-KEY',
                        additionalArguments: '''
                            --scan ./
                        '''
                    )
                }
            }*/ 
         stage('SonarQube Analysis') {
             steps {
                 withSonarQubeEnv('SonarQube-server') {
                     sh '''${SONAR_SCANNER}/bin/SonarScanner -Dsonar.projectName=Ekart \
                     -Dsonar.java.binaries=. \
                     -Dsonar.projectKey=Ekart '''
                 }
             }
           }
    }
    post{
        always{
            echo "Pipeline execution executed"
        }
        unstable{
            echo "Build is unstable. Check OWASP Dependency Check report."
        }
        failure{
            echo "Pipeline Failed"
        }
        success{
            echo "Pipeline completed successfully"
        }
    }
}
