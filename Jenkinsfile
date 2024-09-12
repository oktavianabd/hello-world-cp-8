pipeline {
    agent any

    environment {
        GITHUB_REPO = 'https://github.com/oktavianabd/hello-world-cp-8.git'
        SONARQUBE_URL = 'http://10.63.20.14:9000'
        SONARQUBE_PROJECT_KEY = 'Hello-World'
        SONARQUBE_LOGIN = credentials('sonarqube-analysis-jenkins')  // Add SonarQube token in Jenkins credentials
        ARTIFACTORY_URL = '10.63.20.14:8082'
        ARTIFACTORY_REPO = 'docker-local'
        ARTIFACTORY_CREDENTIALS = credentials('artifactory-publish-jenkins')  // Artifactory credentials
    }

    stages {
        stage('Unit testing') {
            steps {
                // Run unit tests and integration tests
                script {
                    sh '''
                        cd ./backend/
                        npm install
                        npm run test
                    '''
                }
            }
        }
        stage('SonarQube static code analysis') {
            environment {
                scannerHome = tool 'Sonar'
            }
            
            steps {
                withSonarQubeEnv('Default SonarQube Server') {
                    sh '''
                        ${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=Hello-World \
                        -Dsonar.sources=backend/,frontend/,database-init/ \
                        -Dsonar.exclusions=test-output/  \
                        -Dsonar.tests=unit-tests/ \
                        -Dsonar.host.url=${SONARQUBE_URL} \
                        -Dsonar.login=${SONARQUBE_LOGIN} \
                        -Dsonar.javascript.lcov.reportPaths=./test-output/coverage/lcov.info
                    '''
                    }
            }
        }

        stage('Docker Build & Push Frontend') {
            steps {
                script {
                    // Build Docker image
                    sh 'docker build -t hello-world-app -f frontend/Dockerfile frontend'

                    // Tag Docker image for Artifactory
                    sh 'docker tag hello-world-app ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/hello-world-app:latest'

                    // Login to Artifactory
                    sh 'echo ${ARTIFACTORY_CREDENTIALS_PSW} | docker login ${ARTIFACTORY_URL} -u ${ARTIFACTORY_CREDENTIALS_USR} --password-stdin'

                    // Push Docker image to Artifactory
                    sh 'docker push ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/hello-world-app:latest'
                }
            }
        }
    }
}