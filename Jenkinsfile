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
                stage('CI - configuracion de pnpm y node'){
                    steps{
                        sh '''
                            pnpm runtime set node 24 -g
                            pnpm --version
                        '''                    
                    }
                }
                stage('CI - instalacion de dependencias'){
                    steps{
                        sh '''
                            pnpm install
                        '''                    
                    }
                }
                stage('CI - revision de linter'){
                    steps{
                        sh '''
                            pnpm lint
                        '''                    
                    }
                }
                stage('CI - ejecucion de build'){
                    steps{
                        sh '''
                            pnpm build
                        '''                    
                    }
                }
            }
        }
    }
}