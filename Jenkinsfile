pipeline {

    agent any

    tools {
        nodejs 'node'
    }

    stages {

        stage('Checkout') {
            steps {
                checkout scm
            }
        }

        stage('Build') {
            steps {
                sh 'npm install'
            }
        }

        stage('Test') {
            steps {
                sh 'npm test -- --watchAll=false'
            }
        }

        stage('Docker build') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {
                        sh 'docker build -t nodemain:v1.0 .'
                    }

                    else if (env.BRANCH_NAME == 'dev') {
                        sh 'docker build -t nodedev:v1.0 .'
                    }
                }
            }
        }

        stage('Deploy') {
            steps {
                script {

                    if (env.BRANCH_NAME == 'main') {

                        sh '''
                        docker rm -f nodemain-container 2>/dev/null || true
                        docker run -d --name nodemain-container \
                          --expose 3000 \
                          -p 3000:3000 \
                          nodemain:v1.0
                        '''
                    }

                    else if (env.BRANCH_NAME == 'dev') {

                        sh '''
                        docker rm -f nodedev-container 2>/dev/null || true
                        docker run -d --name nodedev-container \
                          --expose 3001 \
                          -p 3001:3000 \
                          nodedev:v1.0
                        '''
                    }
                }
            }
        }
    }
}
