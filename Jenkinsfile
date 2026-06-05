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
    stages {
        stage('Build Image') {
            steps {
                // Obligas a Jenkins a ejecutar este bloque dentro del contenedor con Docker
                container('docker-cli') {
                    sh 'docker ps'
                }
            }
        }
    }
}