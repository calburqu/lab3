pipeline {
    agent none
    stages {
        stage('CI - de nuestra aplicacion de contenedores') {
            agent{
                docker{
                    image 'ghcr.io/pnpm/pnpm:latest'
                    label 'docker'
                }
            }
        }
        stages{
            stage('CI - Instalacion de dependencias'){
                sh '''
                pnpm install
                '''                    
            }
        }

    }
}