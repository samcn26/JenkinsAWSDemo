pipeline {
    agent any
    parameters {
        string(name: 'tomcat_dev', defaultValue: '3.17.130.1', description: 'Staging Server')
        string(name: 'tomcat_prod', defaultValue: '18.217.163.79', description: 'Production Server')
    }

    triggers {
        pollSCM('* * * * *')
    }

    stages {
        stage ('BUild') {
            steps {
                sh 'mvn clean package'
            }

            post {
                success {
                    echo 'Now Archiving'
                    archiveArtifacts artifacts: '**/target/*.war'
                }
            }
        }

        stage ('Deployment') {
            parallel {
                stage ('Deploy to Staging') {
                    steps "scp -i ~/Devops/aws/keyfile/tomcat-demo.pem  **/target/*.war ec2-user@${params.tomcat_dev}:/var/lib/tomcat/webapps"
                }

                stage ('Deploy to Production') {
                    steps "scp -i ~/Devops/aws/keyfile/tomcat-demo.pem  **/target/*.war ec2-user@${params.tomcat_prod}:/var/lib/tomcat/webapps"
                }
            }
        }
    }
}