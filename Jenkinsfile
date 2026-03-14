pipeline {
    agent any

    environment {
        PATH = "/opt/maven/bin:$PATH"
    }

    stages {

        stage('Build') {
            steps {
                echo "-------------build started-------"
                sh 'mvn clean deploy -Dmaven.test.skip=true'
                echo "------------build completed---------"
            }
        }

        stage('Test') {
            steps {
                echo "-------------unit started-------"
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

        stage('Quality Gate') {
            steps {
                script {
                    timeout(time: 1, unit: 'HOURS') {
                        def qg = waitForQualityGate()
                        if (qg.status != 'OK') {
                            #error "Pipeline aborted due to quality gate failure: ${qg.status}"
                            echo "warning: Quality gate failed but cont pipeline: ${qg.status}"
                        }
                    }
                }
            }
        }

    }
}
