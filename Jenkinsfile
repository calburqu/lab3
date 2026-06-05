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
            stages{
                stage('CI - Instalacion de dependencias'){
                    steps{
                        sh "docker --config ~/.docker-anon pull ghcr.io/pnpm/pnpm:latest"
                        sh '''
                            pnpm install
                        '''                    
                    }
                }
            }
        }
    }
}