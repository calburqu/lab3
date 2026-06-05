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
                            KEYCHAIN_PASS="kl154676775"
                            security -v unlock-keychain -p "$KEYCHAIN_PASS" ~/Library/Keychains/login.keychain-db
                            security set-keychain-settings -t 3600 -u ~/Library/Keychains/login.keychain-db
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