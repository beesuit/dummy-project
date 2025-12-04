pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                script {
                    // Make sure Maven is installed on your Jenkins agent or use docker agent with Maven
                    sh 'mvn clean install'
                }
            }
        }

        stage('Test') {
            steps {
                script {
                    sh 'mvn test'
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
}