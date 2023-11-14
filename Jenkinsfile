pipeline{
    agent any
    environment{
        VERSION = "${env.BUILD_ID}"
    }
    stages{
        stage("sonar quality check"){
            
            steps{
                script{
                    withSonarQubeEnv(credentialsId: 'sonar-token') {
                        sh 'chmod +x gradlew'
                        sh './gradlew sonarqube'
    
                    }
                    timeout(time: 1, unit: 'HOURS') {
                      def qg = waitForQualityGate()
                      if (qg.status != 'OK') {
                           error "Pipeline aborted due to quality gate failure: ${qg.status}"
                      }
                    }
                }
                
            }
            
        }
        stage("docker build & docker push") {
            steps{
                script{
                    withCredentials([string(credentialsId: 'docker_pass', variable: 'docker_password')]) {
                        sh '''
                    docker build -t 3.88.70.182:8083/springapp:${VERSION} .
                    docker login -u admin -p harsh1998 3.88.70.182:8083
                    docker push 3.88.70.182:8083/springapp:${VERSION}
                    docker rmi 3.88.70.182:8083/springapp:${VERSION}

                    '''
                    
                }
                    

                    
                }
            }
        }
    }
    
}