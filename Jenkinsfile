pipeline {
    agent any

    environment {
        APP_PORT = '9090'
        JOB_NAME_VAR = "${env.JOB_NAME}"
    }

    stages {
        stage('Build') {
            steps {
                sh 'mvn clean package -DskipTests'
                dir('target') {
                    sh 'ls -al'
                }
            }
        }

        stage('Integration Test') {
            parallel {

                stage('Running Application') {
                    agent any
                    steps {
                        script {
                            try {
                                timeout(time: 60, unit: 'SECONDS') {
                                    dir('target') {
                                        sh 'ls -al'
                                        sh 'java -jar contact.war'
                                    }
                                }
                            } catch (err) {
                                echo "Application stopped (expected due to timeout)"
                                currentBuild.result = 'SUCCESS'
                            }
                        }
                    }
                }

                stage('Running Test') {
                    steps {
                        sleep 30
                        sh 'mvn test -Dtest=RestIT'
                    }
                }
            }
        }
    }
}
