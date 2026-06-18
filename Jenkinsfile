pipeline {
    agent { label 'jdk8' }

    stages {
        stage('Build') {
            steps {
                script {
                    // Make sure Maven is installed on your Jenkins agent or use docker agent with Maven
                    sh '''
                        rsync -a /opt/project/ $WORKSPACE
                        java -version
                        mvn -version

                        cd $WORKSPACE/TAUtilities_3.0.0
                        mvn clean install -DskipTests

                        cd $WORKSPACE/common
                        mvn clean install -DskipTests

                        cd $WORKSPACE/engineering
                        mvn clean install -DskipTests
                        cd $WORKSPACE/engineering/Installer
                        mvn clean assembly:assembly -DskipTests
                        mvn package -f izpack-pom.xml -DskipTests
                        mvn install -f izpack-pom.xml antrun:run -DskipTests

                        if [ ! -f /home/jenkins/.m2/repository/com/oracle/jre/1.6_45x64/jre-1.6_45x64.zip ]; then
                          mvn install:install-file \
                            -Dfile=/home/jenkins/jre-1.6_45x64.zip \
                            -DgroupId=com.oracle \
                            -DartifactId=jre \
                            -Dversion=1.6_45x64 \
                            -Dpackaging=zip
                        fi

                        cd $WORKSPACE/production
                        mvn install -DskipTests
                        mvn package assembly:assembly -DskipTests
                    '''
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    sh '''
                        cd $WORKSPACE/production
                        mvn package -f izpack-pom.xml antrun:run -DskipTests
                    '''
                }
            }
        }
    }

    post {
        success {
            archiveArtifacts artifacts: 'target/*.jar'
          }
        failure {
            sh '''
                echo "Build failed. Sleeping for debugging..."
                sleep 3600
            '''
          }
        always {
            archiveArtifacts artifacts: '**/target/surefire-reports/**', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
          }
      }
}
