@Library('chattingo') _
pipeline {
    agent any
    
    stages {
        stage('Git Clone'){ 
            // Clone repository from GitHub (2 Marks)
            steps{
                script{
                clone("https://github.com/tf-vishal/chattingo.git", "main")
                }
                sh "Copying Git repo successfulll"
            }
        }
    }
}