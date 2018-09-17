pipeline {
    agent any
    
    parameters { 
         string(name: 'key', defaultValue: '/utils/id_rsa/tomcat_demo.pem', description: 'rsakey')
         string(name: 'tomcat_dev', defaultValue: '18.223.235.204', description: 'Staging Server')
         string(name: 'tomcat_prod', defaultValue: '13.59.152.249', description: 'Production Server')
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
                         bat "winscp scp:ec2-user@${params.tomcat_dev}:/var/lib/tomcat7/webapps /privatekey=\"${params.key}\" /upload **/target/*.war"                         
                    }
                }

                stage ("Deploy to Production"){
                    steps {
                         bat "winscp scp:ec2-user@${params.tomcat_prod}:/var/lib/tomcat7/webapps /privatekey=\"${params.key}\" /upload **/target/*.war" 
                    }
                }
            }
        }
    }
}