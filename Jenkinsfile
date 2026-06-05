pipeline {
    agent {
        label 'preferido'
    }
    stages{
        stage("Primer paso pipeline") {
            steps{
                sh 'echo "saludos desde el terminal"'
            }
        }
        stage("Segundo paso paso pipeline") {
            agent {
                label 'container'
            }
            steps{
                sh 'node --version'
            }
        }
        stage("Tercer paso paso pipeline") {
            steps{
                sh 'docker ps'
            }
        }
        stage("Cuarto paso paso pipeline") {
            agent {
                docker {
                    image 'node:22'
                    label 'wsl'
                }
            }
            steps{
                sh 'node --version'
            }
        }
    }
}