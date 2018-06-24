pipeline {
    agent any

    tools{
        maven 'local-maven' 
    }
 
    parameters {
         string(name: 'tomcat_staging', defaultValue: 'ec2-34-244-91-176.eu-west-1.compute.amazonaws.com', description: 'Staging Server')
         string(name: 'tomcat_production', defaultValue: 'ec2-34-244-238-143.eu-west-1.compute.amazonaws.com', description: 'Production Server')
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
                        sh "scp -i /home/jenkins/tomcat.pem **/target/*.war ec2-user@${params.tomcat_staging}:/var/lib/tomcat8/webapps"
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                        sh "scp -i /home/jenkins/tomcat.pem **/target/*.war ec2-user@${params.tomcat_production}:/var/lib/tomcat8/webapps"
                    }
                }
            }
        }
    }
}