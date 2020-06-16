pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }

    parameters {
         string(name: 'tomcat_dev', defaultValue: 'host.docker.internal', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'host.docker.internal', description: 'Production Server')
    }

    triggers {
         pollSCM('* * * * *')
     }

stages{
        stage('Build'){
            steps {
                sh 'mvn clean package'
            }
            post {
                success {
                    echo 'Now Archiving...'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployments'){
            parallel{
                stage ('Deploy to Staging'){
                    steps {
                        sh "cp **/target/*.war /tomcat-staging/"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "cp **/target/*.war /tomcat-prod/"
                    }
                }
            }
        }
    }
}