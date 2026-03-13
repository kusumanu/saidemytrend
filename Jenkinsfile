pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('Build') {
            steps {
                sh 'mvn clean deploy'
            }
        }

        stage('SonarQube Analysis') {
            environment {
                scannerHome = tool 'sonarscanar'
            }
            steps {
                withSonarQubeEnv('manusonarcloud') {
                    sh "${scannerHome}/bin/sonar-scanner"
                }
            }
        }

    }
}
