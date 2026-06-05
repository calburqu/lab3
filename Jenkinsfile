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
                        sh '''
                            security -v unlock-keychain -p "kl154676775" ~/Library/Keychains/login.keychain-db
                            docker pull ghcr.io/pnpm/pnpm:latest
                        '''
                        sh '''
                            pnpm install
                        '''                    
                    }
                }
            }
        }
    }
}