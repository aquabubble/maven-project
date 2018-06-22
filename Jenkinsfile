pipeline {
    agent any

    tools{
        maven 'local-maven' 
    }
 
    parameters {
         string(name: 'tomcat-staging', defaultValue: '35.166.210.154', description: 'Staging Server')
         string(name: 'tomcat-production', defaultValue: '34.244.238.143', description: 'Production Server')
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
                        sh "scp -i /home/aquabubble/Downloads/tomcat.pem **/target/*.war ec2-user@${params.tomcat-staging}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/aquabubble/Downloads/tomcat.pem **/target/*.war ec2-user@${params.tomcat-production}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}