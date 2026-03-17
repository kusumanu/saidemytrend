def registry = 'https://trial5ecsjs.jfrog.io/'
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
                            echo "warning: Quality gate failed but cont pipeline: ${qg.status}"
                        }
                    }
                }
            }
        }

        stage("Jar Publish") {
            steps {
                script {
                    echo '<--------------- Jar Publish Started --------------->'

                    def server = Artifactory.newServer(
                        url: registry + "/artifactory",
                        credentialsId: "trial5ecsjs.jfrog.io"
                    )

                    def properties = "buildid=${env.BUILD_ID},commitid=${GIT_COMMIT}"

                    def uploadSpec = """{
                          "files": [
                            {
                              "pattern": "jarstaging/(*)",
                              "target": "manu-libs-release-local/{1}",
                              "flat": "false",
                              "props": "${properties}",
                              "exclusions": [ "*.sha1", "*.md5"]
                            }
                         ]
                     }"""

                    def buildInfo = server.upload(uploadSpec)
                    buildInfo.env.collect()
                    server.publishBuildInfo(buildInfo)

                    echo '<--------------- Jar Publish Ended --------------->'
                }
            }
        }

    }
}
