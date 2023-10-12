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
    }
}