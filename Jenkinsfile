pipeline {

    agent {
        node {
            label 'built-in'
            customWorkspace '/mnt/war'
        }
    }

    stages {

        stage('SCM') {
            steps {
                sh '''
                rm -rf project-1
                git clone https://github.com/nageshjdevops/project-1.git
                '''
            }
        }

        stage('BUILD') {
            steps {
                sh '''
                cd /mnt/war/project-1

                mvn clean package

                aws s3 cp target/LoginWebApp.war s3://war-loginwebapp/
                '''
            }
        }

        stage('DEPLOY') {

            agent {
                node {
                    label 'slave-2'
                    customWorkspace '/mnt/slave'
                }
            }

            steps {

                sh '''
                cd /mnt/slave

                rm -f LoginWebApp.war

                aws s3 cp s3://war-loginwebapp/LoginWebApp.war .

                cp LoginWebApp.war /mnt/apache-tomcat-10.1.55/webapps/
                '''
            }
        }
    }
}
