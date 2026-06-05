pipeline {
    agent {
        kubernetes {
            yaml '''
apiVersion: v1
kind: Pod
metadata:
  labels:
    some-label: jenkins-build
spec:
  containers:
  - name: docker-cli
    image: docker:24.0.7-cli
    command: ['cat']
    tty: true
    volumeMounts:
    - mountPath: /var/run/docker.sock
      name: docker-sock
  volumes:
  - name: docker-sock
    hostPath:
      path: /var/run/docker.sock
'''
        }
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
            agent { label 'wsl'}
            }
            tools {
                nodejs 'node22'
            }
            steps{
                sh 'node --version'
            }
        }                
    }

}