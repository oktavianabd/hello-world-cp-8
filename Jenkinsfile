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
        DEPLOYMENT_SERVER = '10.63.20.14'
        SSH_CREDENTIALS = credentials('20-14-deploy-jenkins')  // SSH credentials
    }

    stages {
        // stage('Unit testing') {
        //     steps {
        //         // Run unit tests and integration tests
        //         script {
        //             sh '''
        //                 cd ./backend/
        //                 npm install
        //                 npm run test
        //             '''
        //         }
        //     }
        // }
        // stage('SonarQube static code analysis') {
        //     environment {
        //         scannerHome = tool 'Sonar'
        //     }
            
        //     steps {
        //         withSonarQubeEnv('Default SonarQube Server') {
        //             sh '''
        //                 ${scannerHome}/bin/sonar-scanner \
        //                 -Dsonar.projectKey=Hello-World \
        //                 -Dsonar.sources=backend/,frontend/,database-init/ \
        //                 -Dsonar.exclusions=test-output/  \
        //                 -Dsonar.tests=unit-tests/ \
        //                 -Dsonar.host.url=${SONARQUBE_URL} \
        //                 -Dsonar.login=${SONARQUBE_LOGIN} \
        //                 -Dsonar.javascript.lcov.reportPaths=./test-output/coverage/lcov.info
        //             '''
        //             }
        //     }
        // }

        // stage('Docker Build & Push Docker Images') {
				// 		parallel {
				// 				stage('Frontend') {
				// 						steps {
				// 								script {
				// 										// VERSION=$(grep '"version":' ./backend/package.json | head -1 | awk -F: '{ print $2 }' | sed 's/[", ]//g')
				// 										sh '''
				// 										docker build -t frontend -f frontend/Dockerfile frontend
				// 										docker tag frontend ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/frontend:latest
				// 										echo ${ARTIFACTORY_CREDENTIALS_PSW} | docker login ${ARTIFACTORY_URL} -u ${ARTIFACTORY_CREDENTIALS_USR} --password-stdin
				// 										docker push ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/frontend:latest
				// 										'''
				// 								}
				// 						}
				// 				}

				// 				stage('Backend') {
				// 						steps {
				// 								script {
				// 										// VERSION=$(grep '"version":' ./backend/package.json | head -1 | awk -F: '{ print $2 }' | sed 's/[", ]//g')
				// 										sh '''
				// 										docker build -t backend -f backend/Dockerfile backend
				// 										docker tag backend ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/backend:latest
				// 										echo ${ARTIFACTORY_CREDENTIALS_PSW} | docker login ${ARTIFACTORY_URL} -u ${ARTIFACTORY_CREDENTIALS_USR} --password-stdin
				// 										docker push ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/backend:latest
				// 										'''
				// 								}
				// 						}
				// 				}
				// 		}
    		// }

        stage('Deploy to Server') {
            steps {
                sshCommand(
                    remote: [ 
												user: 'root',
												name: 'deploy-server',
                        host: '${DEPLOYMENT_SERVER}', 
                        credentialsId: '${SSH_CREDENTIALS}' 
                    ],
                    command: '''
                        docker pull ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/frontend:latest
                        docker pull ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/backend:latest
                        docker stop frontend || true
                        docker rm frontend || true
                        docker run -d --name frontend -p 80:80 ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/frontend:latest
                        docker stop backend || true
                        docker rm backend || true
                        docker run -d --name backend -p 3000:3000 ${ARTIFACTORY_URL}/${ARTIFACTORY_REPO}/backend:latest
                    '''
                )
            }
        }
				

		}
}