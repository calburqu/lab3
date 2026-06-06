pipeline {
    agent none
    environment {
        IMAGE_NAME = 'lab3'
        DH_REPO    = 'calburqu/lab3'
        GH_REPO    = 'ghcr.io/calburqu/lab3'
    }
    stages {
        stage('CI - Preparando ambiente') {
            agent {
                kubernetes {
                    yaml '''
spec:
  containers:
  - name: pnpm
    image: ghcr.io/pnpm/pnpm:latest
    command: ["cat"]
    tty: true
'''
                }
            }
            environment {
                PATH = "/pnpm/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
            }            
            stages {
                stage('CI - Install') {
                    steps {
                        container('pnpm') {
                            sh '''
                                pnpm runtime set node 24 -g
                            '''
                            sh 'pnpm install'
                        }
                    }
                }
                stage('CI - Test') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm test'
                        }
                    }
                }                
                stage('CI - Build') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm build'
                        }
                    }
                }
            }
        }

        stage('CD - Push') {
            agent {
                kubernetes {
                    yaml '''
spec:
  containers:
  - name: docker-cli
    image: docker:24.0.7-cli
    command: ["cat"]
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
            steps {
                container('docker-cli') {
                    sh '''
                        docker build -t ${IMAGE_NAME}:latest .
                        docker tag ${IMAGE_NAME}:latest ${DH_REPO}:latest
                        docker tag ${IMAGE_NAME}:latest ${GH_REPO}:latest
                    '''
                    
                    withCredentials([
                        usernamePassword(credentialsId: 'dh-credencial', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASSWORD'),
                        usernamePassword(credentialsId: 'gh-credencial', usernameVariable: 'GH_USER', passwordVariable: 'GH_PASSWORD')
                    ]) {
                        sh '''
                            # Docker Hub Login y Push
                            echo "$DH_PASSWORD" | docker login -u "$DH_USER" --password-stdin
                            docker push ${DH_REPO}:latest
                            
                            # Forzar cierre de sesión previo e iniciar con entorno limpio para GHCR
                            docker logout ghcr.io || true
                            
                            # Crear un archivo de configuración temporal de Docker aislado para este comando
                            export DOCKER_CONFIG=\$(mktemp -d)
                            
                            echo "$GH_PASSWORD" | docker login -u "$GH_USER" ghcr.io --password-stdin
                            docker push ${GH_REPO}:latest
                            
                            # Limpieza del entorno temporal
                            rm -rf \$DOCKER_CONFIG
                        '''
                    }
                }
            }
        }

        stage('CD - despliegue en k8') {
            agent {
                kubernetes {
                    yaml '''
spec:
  containers:
  - name: kubectl
    image: alpine/k8s:1.34.1
    command: ["cat"]
    tty: true
'''
                }
            }
            steps {
                container('kubectl') {
                    sh 'kubectl version --client'
                }
            }
        }
    }
}