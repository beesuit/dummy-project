pipeline {
    agent { label 'jdk8' }

    stages {
        stage('Build') {
            steps {
                script {
                    // Make sure Maven is installed on your Jenkins agent or use docker agent with Maven
                    sh '''
                        rsync -a /opt/project/ $WORKSPACE
                        cd $WORKSPACE/TAUtilities_3.0.0
                        java -version
                        mvn -version
                        mvn clean install -DskipTests
                    '''
                }
            }
        }

        stage('Package') {
            steps {
                script {
                    sh 'mvn package'
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
