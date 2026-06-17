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
                        mvn -U clean install -DskipTests
                        cd $WORKSPACE/engineering/Installer
                        mvn clean assembly:assembly -DskipTests
                        mvn package -f izpack-pom.xml -DskipTests
                        mvn install -f izpack-pom.xml antrun:run -DskipTests

                        cd $WORKSPACE/production
                        mvn -U clean install -DskipTests
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
                        echo "HOME=$HOME"
                        mvn help:evaluate -Dexpression=settings.localRepository -q -DforceStdout
                        whoami
                        mvn -U package -f izpack-pom.xml antrun:run -DskipTests
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
