pipeline{
    agent any
    environment {
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("docker build & docker push"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus', variable: 'nex')]) {
                        sh '''
                                docker build -t 172.16.1.30:8083/springapp:${VERSION} .
                                docker login -u admin -p $nex 172.16.1.30:8083
                                docker push 172.16.1.30:8083/springapp:${VERSION}
                                docker rmi 172.16.1.30:8083/springapp:${VERSION}
                            '''
                    }
                }
            }
        }
        stage("pushing the helm chart to nexus"){
            steps{
                script{
                    withCredentials([string(credentialsId: 'nexus', variable: 'nex')]) {
                        dir('kubernetes/') {
                            def helmversion = sh(script: "helm show chart myapp | grep version | cut -d: -f 2 | tr -d ' '", returnStdout: true).trim()
                            sh 'tar -czvf myapp-${helmversion}.tgz myapp/'  
                            sh 'curl -u admin:$nex http://172.16.1.30:8081/repository/helm-hosted/ --upload-file myapp-${helmversion}.tgz -v'
                        }
                    }
                }
            } 
        }
        stage('Deploy Application on kubernetes') {
            steps {
                dir ("kubernetes/"){ 
                    sh 'helm list'
				    sh 'helm upgrade --install --set image.repository="172.16.1.30:8083/springapp" --set image.tag="${VERSION}" myjavaapp myapp/ ' 
                }
            }
        }
    }
}