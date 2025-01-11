pipeline{
    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
        SCANNER_HOME = tool 'sonar-scanner'
    }
    stages{
        stage("Sonarqube"){
            agent {
                docker {
                    image 'openjdk:11'
                }
            }
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-pass') {
                        sh '$SCANNER_HOME/bin/sonar-scanner -Dsonar.projectKey=my-app -Dsonar.sources=src -Dsonar.host.url=http://34.226.192.62:9000/'
                    }
                }
            }   
        }
        stage("Quality Gate"){
            steps{
                script {
                    def Qualitygate = waitForQualityGate()
                    if (QualityGate.status != 'OK') {
                        error "abort pipeline: ${qualityGate.status}"
                    }
                }
            }   
        }
    }
}
