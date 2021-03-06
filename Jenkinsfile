pipeline {
    agent any
    
    parameters { 
         
         string(name: 'tomcat_dev', defaultValue: 'ec2-18-223-248-109.us-east-2.compute.amazonaws.com', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: 'ec2-18-191-194-126.us-east-2.compute.amazonaws.com', description: 'Production Server')
    } 

    triggers {
         pollSCM('* * * * *') // Polling Source Control
     }

stages{
        stage('Build'){
            steps {
                bat 'mvn clean package'
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
                         bat "winscp scp:ec2-user@${params.tomcat_dev}:/var/lib/tomcat8/webapps /privatekey=\"/utils/id_rsa/tomcat_demo.ppk\" /upload **/target/*.war -hostkey=\"ssh-ed25519 256 vmupt4u/NMltLRvc3q4qnwjEd+kowlwrizZgPcpRAvk=\" close exit"                         
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                         bat "winscp scp:ec2-user@${params.tomcat_prod}:/var/lib/tomcat8/webapps /privatekey=\"/utils/id_rsa/tomcat_prod.ppk\" /upload **/target/*.war  -hostkey=\"ssh-ed25519 256 iSgD6xqziRbcEY5LZVw4MJt3GSt59ogtpjQm/4uoTxY=\" close exit"
                    }
                }
            }
        }
    }
}