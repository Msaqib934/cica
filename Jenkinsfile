pipeline{
    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
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
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
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
        stage("Push Code to Nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nex', variable: 'nexus')]) {
                        sh '''
                            docker build -t 34.235.165.153:8083/springapp:${VERSION} .
                            docker login -u admin -p $docker-variable 34.235.165.153:8083
                            docker push 34.235.165.153:8083/springapp:${VERSION}
                            docker rmi 34.235.165.153:8083/springapp:${VERSION}
                        '''
                    }  
                }
            }   
        }
        stage("Push Helm to Nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nex', variable: 'nexus')]) {
                        dir {'kubernetes/'} {
                            sh '''
                                def helmversion = sh(script: "helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' '", returnStdout: true).trim()
                                sh 'tar -czvf myapp-${helmversion}.tgz myapp/'  
                                sh 'curl -u admin:$nex http://34.235.165.153:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v'
                        '''
                        } 
                    }  
                }
            }   
        }
    }
}
