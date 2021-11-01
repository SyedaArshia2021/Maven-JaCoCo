pipeline {
    agent any
    environment{
        TOMCAT_HOME = "/opt/tomcat/apache-tomcat-9.0.54"
        TOMCAT_HOST="10.0.2.15"
    }
    tools {
        maven 'mvn_3.5'
    }
    stages{
        stage ('Initialize') {
            steps {
                sh '''
                    echo "PATH = ${PATH}"
                    echo "M2_HOME = ${M2_HOME}"
                    echo "JAVA_HOME = ${JAVA_HOME}"
                '''
            }

        }

        stage ('Build') {
            steps {
                sh 'mvn clean package'
            }
        }
        stage ('Deploy') {
            steps {
                sh 'sudo cp target/RestDemo-0.0.1-SNAPSHOT.war ${TOMCAT_HOME}/webapps/'
            }
        }
        stage ('Start tomcat') {
            steps {
                sh '"export CATALINA_BASE=/opt/tomcat/apache-tomcat-9.0.54"'
                sh '"sudo ${TOMCAT_HOME}/bin/catalina.sh start"'
            }
        }
        stage ('Functional tests') {
            steps {
                sh "mvn verify -Pstaging -Dtomcat.host=${TOMCAT_HOST}"
            }
            post {
                success {
                    junit 'target/**/*.xml'
                    jacoco(execPattern: 'target/jacoco.exec')
                }
            }
        }
        stage ('Stop tomcat') {
            steps {
                sh '"sudo ${TOMCAT_HOME}/bin/catalina.sh stop"'
            }
        }
    }
}
