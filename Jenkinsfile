pipeline {

    agent none

    stages {

        stage('SCM & BUILD') {
            agent {
                node {
                    label 'built-in'
                    customWorkspace '/mnt/war'
                }
            }

            steps {
                sh '''
                rm -rf *
                git clone https://github.com/nageshjdevops/project-1.git

                cd project-1

                mvn clean package

                aws s3 cp target/LoginWebApp.war s3://war-loginwebapp/
                '''
            }
        }

        stage('DEPLOY') {
            agent {
                node {
                    label 'slave-2'
                    customWorkspace '/home/ec2-user/jenkins'
                }
            }

            steps {
                sh '''
                rm -f LoginWebApp.war

                aws s3 cp s3://war-loginwebapp/LoginWebApp.war .

                rm -rf /mnt/apache-tomcat-10.1.55/webapps/LoginWebApp
                rm -f /mnt/apache-tomcat-10.1.55/webapps/*.war

                cp LoginWebApp.war /mnt/apache-tomcat-10.1.55/webapps/

                sleep 15

                ls -ltr /mnt/apache-tomcat-10.1.55/webapps/
                '''
            }
        }

        stage('VERIFY') {
            agent {
                node {
                    label 'slave-2'
                }
            }

            steps {
                sh '''
                curl -I http://localhost:8080 || true
                '''
            }
        }
    }

    post {
        success {
            echo 'BUILD SUCCESSFUL - WAR uploaded to S3 and deployed to Tomcat'
        }

        failure {
            echo 'PIPELINE FAILED'
        }
    }
}
