pipeline {

    agent {
        node {
            label 'slave-1'
            customWorkspace '/mnt/slave'
        }
    }

    stages {

        stage('SCM') {
            steps {
                sh '''
                rm -rf /mnt/slave/project
                git clone https://github.com/YOUR_GITHUB/project.git
                '''
            }
        }

        stage('BUILD') {
            steps {
                sh '''
                cd /mnt/slave/project

                /mnt/apache-maven-3.9.16/bin/mvn clean package

                cd target

                aws s3 cp LoginWebApp.war s3://YOUR_BUCKET/
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

                aws s3 cp s3://YOUR_BUCKET/LoginWebApp.war .

                cp LoginWebApp.war /mnt/apache-tomcat-10.1.55/webapps/
                '''
            }
        }
    }
}
