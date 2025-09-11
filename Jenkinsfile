@Library('chattingo') _
pipeline {
    agent any

    environment {
        FRONTEND_IMAGE = "tfvishal/chattingo-frontend-image"
        BACKEND_IMAGE  = "tfvishal/chattingo-backend-image"
        BUILD_TAG      = "${env.BUILD_NUMBER}"
        FRONTEND_PATH  = "./frontend"
        BACKEND_PATH   = "./backend"
    }

    tools {
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
                script {
                    imageBuild(FRONTEND_IMAGE, FRONTEND_PATH)
                    imageBuild(BACKEND_IMAGE, BACKEND_PATH)
                }
            }
        }

        stage('Image Scan') {
            steps {
                script {
                    imageScan(FRONTEND_IMAGE)
                    imageScan(BACKEND_IMAGE)
                }
            }
        }

        stage('Push Image') {
            steps {
                script {
                    docker_push(FRONTEND_IMAGE)
                    docker_push(BACKEND_IMAGE)
                }
            }
        }

        stage('Loading Env Files') {
            steps {
                script {
                    envLoading("frontend", FRONTEND_PATH)
                    envLoading("backend", BACKEND_PATH)
                }
            }
        }

        stage('Deploy Image') {
            steps {
                script {
                    try{
                         deployImage(FRONTEND_IMAGE)
                    deployImage(BACKEND_IMAGE)
                    } catch (err){
                        env.BUILD_STATUS = 'true'
                        }   
                }
            }
        }
        stage('Rollback'){
                when { expression{
                        env.BUILD_STATUS == 'true'
                    }  
                }
                steps{
                    script{
                        rollback(FRONTEND_IMAGE)
                        rollback(BACKEND_IMAGE  )
                    }
                }
            }

    }
    post{
        always{
            echo "CLEANIGN UP RESEDUAL IMAGES"
            sh "docker image prune -f"
        }
    }
}
