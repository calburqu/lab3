pipeline {
    agent none
    environment {
        IMAGE_NAME = 'lab3'
        DH_REPO    = 'calburqu/lab3'
        GH_REPO    = 'ghcr.io/calburqu/lab3'
    }
    stages {
        stage('CI - de nuestra aplicacion de contenedores') {
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
                stage('CI - configuracion de pnpm y node') {
                    steps {
                        container('pnpm') {
                            sh '''
                                pnpm runtime set node 24 -g
                                pnpm --version
                            '''
                        }
                    }
                }
                stage('CI - instalacion de dependencias') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm install'
                        }
                    }
                }
                stage('CI - revision de linter') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm lint'
                        }
                    }
                }
                stage('CI - test') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm test'
                        }
                    }
                }                
                stage('CI - ejecucion de build') {
                    steps {
                        container('pnpm') {
                            sh 'pnpm build'
                        }
                    }
                }
            }
        }

        stage('CD - empaquetado y distribucion') {
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
                    // 1. Construcción de imágenes
                    sh '''
                        docker build -t ${IMAGE_NAME}:latest .
                        docker tag ${IMAGE_NAME}:latest ${DH_REPO}:latest
                        docker tag ${IMAGE_NAME}:latest ${GH_REPO}:latest
                    '''
                    
                    // 2. Login y Push Manual (Evita el fallo de docker.withRegistry en K8s)
                    withCredentials([
                        usernamePassword(credentialsId: 'dh-credencial', usernameVariable: 'DH_USER', passwordVariable: 'DH_PASSWORD'),
                        usernamePassword(credentialsId: 'gh-credencial', usernameVariable: 'GH_USER', passwordVariable: 'GH_PASSWORD')
                    ]) {
                        sh '''
                            echo "$DH_PASSWORD" | docker login -u "$DH_USER" --password-stdin
                            docker push ${DH_REPO}:latest
                            
                            echo "$GH_PASSWORD" | docker login -u "$GH_USER" ghcr.io --password-stdin
                            docker push ${GH_REPO}:latest
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