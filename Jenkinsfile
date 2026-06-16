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

                        cd $WORKSPACE/production
                        mvn clean install -DskipTests
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
        always {
            archiveArtifacts artifacts: '**/target/surefire-reports/**', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
          }
      }
}
