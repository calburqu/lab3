# Datos docker hub
Token docker hub

- docker login -u calburqu

- dckr_pat_l4SPDYfF3Kl0ILMz6eTm_OCVbC0

# Datos github
Token github

- docker login -u calburqu ghcr.io

- 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

# Dockerfile

Para compilar la imagen del Dockerfile:

- docker build -t lab3:latest .

Para revisar la imagen

- docker image ls|grep lab3

Para probar la imagen

- docker run --rm -dp 50000:3000 lab3:latest

- curl localhost:50000/lab 

 
## Manifiesto kubernetes

El deployment del manifiesto kubernetes usa una imagen alojada en dockerhub

- image: docker.io/calburqu/lab3:latest

Compilar manifiesto kubernetes
- kubectl apply -f entrega.yaml

Para revisar los pods creados

- kubectl get pods -n ns-cesar-alburquenque

Para revisar el secreto creado

- kubectl get secret -n ns-cesar-alburquenque

Para revisar el configmap

- kubectl describe configmap config-cesar-alburquenque -n ns-cesar-alburquenque

Para revisar el deployment

- kubectl get deployment -n ns-cesar-alburquenque   

Para revisar el service

- kubectl get svc -n ns-cesar-alburquenque

Para revisar el log del deployment

- kubectl logs deployment/app-cesar-alburquenque -n ns-cesar-alburquenque

Para revisar las variables de ambiente

- kubectl exec deployment/app-cesar-alburquenque -n ns-cesar-alburquenque -- printenv
