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
    }
}