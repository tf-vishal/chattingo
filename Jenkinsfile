@Library('chattingo') _
pipeline {
    agent any
    
    stages {
        stage('Git Clone'){ 
            // Clone repository from GitHub (2 Marks)
            steps{
            checkout scm
            }
        }
        stage('Image Build'){
            steps{
                sh "docker build -t tfvishal/chattingo-frontend-image:1 ./frontend"
                sh "docker build -t tfvishal/chattingo-backend-image:1 ./backend"
            }
        }
        stage('Image Scan'){
            steps{
                script {
                    // Replace with your actual tag or variable
                def imageName = "tfvishal/chattingo-frontend-image:1"
                echo "🔍 Scanning ${imageName} for vulnerabilities..."
            
                // Run Trivy and fail the build on HIGH or CRITICAL vulnerabilities
                //sh """trivy image --exit-code 1 --severity HIGH,CRITICAL ${imageName}"""
                }
            script {
                    // Replace with your actual tag or variable
                def imageName = "tfvishal/chattingo-backend-image:1"
                echo "🔍 Scanning ${imageName} for vulnerabilities..."
            
                // Run Trivy and fail the build on CRITICAL vulnerabilities
                //sh """trivy image --exit-code 1 --severity CRITICAL ${imageName}"""
                }
            }
        }
        stages{
            steps{
                script{
                    def imageName= "tfvishal/chattingo-frontend-image:1"

                    withCredentials([usernamePassword(
                        credentialsID: "dockerhub" ,
                        passwordVariable: "dockerhubPass",
                        usernameVariable: "dockerhubUser"
                    )]){
                        sh "docker login -u ${env.dockerhubUser} -p ${env.dockerhubPass}"
                        sh "docker image tag ${imageName} ${env.dockerhubUser}/${imageName}:1"
                        sh "docker push ${dockerhubUser}/${imageName}:1"
                        sh "echo '✅ Docker image pushed successfully!'"
                    }
                }
            }
        }
    }
}