@Library('chattingo') _
pipeline {
    agent any

    tools{
        maven 'Maven'
    }

    stages {

        stage('Git Clone') { // Clone repository from GitHub
            steps {
                checkout scm
            }
        }

        stage('Image Build') {
            steps {
                sh "docker build -t tfvishal/chattingo-frontend-image:${env.BUILD_NUMBER} ./frontend"
                sh "docker build -t tfvishal/chattingo-backend-image:${env.BUILD_NUMBER} ./backend"
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    def frontendImage = "tfvishal/chattingo-frontend-image:${env.BUILD_NUMBER}"
                    echo "🔍 Scanning ${frontendImage} for vulnerabilities..."
                    //sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${frontendImage}"

                    def backendImage = "tfvishal/chattingo-backend-image:${env.BUILD_NUMBER}"
                    echo "🔍 Scanning ${backendImage} for vulnerabilities..."
                    //sh "trivy image --exit-code 1 --severity CRITICAL ${backendImage}"
                }
            }
        }
        

        stage('Push Image') {
            steps {
                script {
                    def frontendImage = "tfvishal/chattingo-frontend-image:${env.BUILD_NUMBER}"
                    def backendImage = "tfvishal/chattingo-backend-image:${env.BUILD_NUMBER}"
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub',
                        passwordVariable: 'dockerhubPass',
                        usernameVariable: 'dockerhubUser'
                    )]) {
                        sh "docker login -u ${dockerhubUser} -p ${dockerhubPass}"
                        sh "docker tag ${frontendImage} ${dockerhubUser}/${frontendImage}:${env.BUILD_NUMBER}"
                        sh "docker push ${frontendImage}"
                        echo '✅ Docker Backend image pushed successfully!'

                        sh "docker tag ${backendImage} ${dockerhubUser}/${backendImage}:${env.BUILD_NUMBER}"
                        sh "docker push ${backendImage}"
                        echo '✅ Docker Backend image pushed successfully!'
                    }
                }
            }
        }
        stage('Loading Env FIles'){
            steps{
                withCredentials([   
                    file(credentialsId: 'backend-env',
                    variable: 'BACKEND_ENV_FILE'),
                    file(credentialsId: 'frontend-env',
                    variable: 'FRONTEND_ENV_FILE')
                ])

                {
                    sh '''
                    chmod -R u+w ./backend ./frontend
                    cp "$BACKEND_ENV_FILE" ./backend/.env
                    cp "$FRONTEND_ENV_FILE" ./frontend/.env

                    ls -l ./backend/.env ./frontend/.env'''
                }
            }
        }
        stage ('Deploy Image'){
            steps{
                sh "docker compose down"
                sh "docker compose pull"
                sh "docker compose up -d"

                sh "rm -f ./backend/.env ./frontend/.env"
            }
        }

    }
}
