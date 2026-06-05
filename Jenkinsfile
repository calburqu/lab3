pipeline {
    agent none
    environment{
        IMAGE_NAME = 'lab3'
        DH_REPO = 'calburqu/lab3'
        GH_REPO = 'ghcr.io/calburqu/lab3'
    }
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
                stage('CI - test'){
                    steps {
                        sh '''
                            pnpm test
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
        stage('CD - empaquetado y distribucion') {
            agent { label 'docker'}
            steps{
                sh '''
                    docker build -t ${IMAGE_NAME} .
                    docker tag ${IMAGE_NAME} ${DH_REPO}
                    docker tag ${IMAGE_NAME} ${GH_REPO}
                '''
                script{
                    docker.withRegistry('https://index.docker.io','dh-credencial'){
                        sh 'docker push ${DH_REPO}'
                    }
                    docker.withRegistry('https://ghcr.io','gh-credencial'){
                        sh 'docker push ${GH_REPO}'
                    }
                }
            }

        }
    }
}