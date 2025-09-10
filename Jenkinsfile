@Library('chattingo') _
pipeline {
    agent any

    stages {

        stage('Git Clone') { // Clone repository from GitHub
            steps {
                checkout scm
            }
        }

        stage('Image Build') {
            steps {
                sh "docker build -t tfvishal/chattingo-frontend-image:1 ./frontend"
                sh "docker build -t tfvishal/chattingo-backend-image:1 ./backend"
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    def frontendImage = "tfvishal/chattingo-frontend-image:1"
                    echo "🔍 Scanning ${frontendImage} for vulnerabilities..."
                    //sh "trivy image --exit-code 1 --severity HIGH,CRITICAL ${frontendImage}"

                    def backendImage = "tfvishal/chattingo-backend-image:1"
                    echo "🔍 Scanning ${backendImage} for vulnerabilities..."
                    //sh "trivy image --exit-code 1 --severity CRITICAL ${backendImage}"
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    def frontendImage = "tfvishal/chattingo-frontend-image:1"
                    withCredentials([usernamePassword(
                        credentialsId: 'dockerhub',
                        passwordVariable: 'dockerhubPass',
                        usernameVariable: 'dockerhubUser'
                    )]) {
                        sh "docker login -u ${dockerhubUser} -p ${dockerhubPass}"
                        sh "docker tag ${frontendImage} ${dockerhubUser}/${frontendImage}"
                        sh "docker push ${frontendImage}"
                        echo '✅ Docker image pushed successfully!'
                    }
                }
            }
        }

    }
}
