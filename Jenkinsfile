pipeline{
    agent any

    stages{
          stage('Build Stage'){
            steps{
                withMaven(maven:'maven3'){
                    sh 'mvn -f FirstJavaProgram/pom.xml clean install'
                }
            }
        }
        stage('SonarQube Analysis'){
            steps{
                withSonarQubeEnv('sonar'){
                    sh "mvn -f FirstJavaProgram/pom.xml  sonar:sonar"


            }
            }
        }

        stage('Deploy to artifactory'){
           steps{
              rtServer(
                id: "artifactory-server"
                )
                rtUpload(
                    serverId: "artifactory-server",
                    spec: '''{
                            "files":[
                            {
                                "pattern":"*.war",
                                "target":"assignment-3-libs-snapshot-local"
                             }
                                     ]
                    }'''
                )
                rtPublishBuildInfo(
                serverId: "artifactory-server"
                )
           }
        }
        stage('Build docker image'){
        steps{
        sh 'docker build -t 020399/firstjavaprogram:latest .'
        }
        }
            stage('Push docker image'){
        steps{
        withCredentials([string(credentialsId: 'docker', variable: 'dockercred')]) {
        sh "docker login -u 020399 -p ${dockercred}"
}
    sh 'docker push 020399/firstjavaprogram:latest'
        }
        }
    }
}
