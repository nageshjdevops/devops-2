pipeline {

    agent none

    stages {

        stage('SCM & BUILD on Master') {
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
                ls -ltr target/*.war
                '''
            }
        }

        stage('Copy WAR to Slave') {
            agent {
                node {
                    label 'slave-2'
                    customWorkspace '/home/ec2-user/jenkins'
                }
            }

            steps {
                sh '''
                rm -rf *
                '''
            }
        }

        stage('Deploy on Slave') {
            agent {
                node {
                    label 'slave-2'
                    customWorkspace '/home/ec2-user/jenkins'
                }
            }

            steps {
                sh '''
                cp /mnt/war/project-1/target/*.war /home/ec2-user/jenkins/

                ls -ltr

                sudo cp *.war /root/apache-tomcat-10.1.55/webapps/

                sleep 10

                curl -I http://localhost:8080 || true
                '''
            }
        }
    }

    post {
        success {
            echo 'Application deployed successfully'
        }
        failure {
            echo 'Pipeline failed'
        }
    }
}
