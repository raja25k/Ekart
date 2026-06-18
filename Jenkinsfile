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
                     sh '''${SONAR_SCANNER}/bin/sonar-scanner -Dsonar.projectName=Ekart \
                     -Dsonar.java.binaries=. \
                     -Dsonar.projectKey=Ekart '''
                 }
             }
           }
        stage('Docker Build & Deploy') {
             steps {
                withDockerRegistry(credentialsId: 'docker-token') {
                    sh "docker build -t shopping-cart -f docker/Dockerfile ."
                    sh "docker tag shopping-cart raja53a/shopping-cart:latest"
                    sh "docker push raja53a/shopping-cart:latest"
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
