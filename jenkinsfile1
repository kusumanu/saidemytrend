pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('Build') {
            steps {
                echo "-------------build startred-------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------------build completed---------"
            }
        }
        stage('test') {
            steps {
                echo "-------------unit startred-------"
                sh 'mvn surefire-report:report'
                echo "------------unit completed---------"
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
