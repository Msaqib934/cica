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
                    image 'openjdk:17'
                }
            }
            steps{
                script {
                    withSonarQubeEnv(credentialsId: 'sonar-pass') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube --debug'
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
