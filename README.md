# Formation Docker

proposé par [doussou-formation.com](https://www.doussou-formation.com/formation/formation-kubernetes/)

Cette formation Kubernetes s'adresse aux differents professionnels TI qui souhaitent mettre en place un système d'orchestration de conteneur.
La formation ne demande pas ou peu de connaissance technique. Le formateur prendra le temps d'installer un environnement complet pour permettre la mise en place et la configuration des conteneurs et de leurs gestion

Besoins technologiques pour la formation en ligne: avoir un ordinateur connecté à internet et un microphone.

# Information pour la formation

ce repository contient les differentes lignes de commandes et fichiers necessaires pour les differents exercices et pratique de la formation

# Requis

la formation necessite l'installation de docker desktop ou d'un equivalent.
On peut retrouver Docker Desktop sur le site officiel de [Docker](https://www.docker.com/products/docker-desktop)

# Installation de minikube

Si vous avez la version docker toolbox, kubernetes n'est pas disponible dans cette version de Docker.
Vous pouvez installer minikube pour utiliser kubernetes dans votre environnement

Lancer la commande suivante pour recuperer et installer minikube

```bash
New-Item -Path 'c:\' -Name 'minikube' -ItemType Directory -Force
Invoke-WebRequest -OutFile 'c:\minikube\minikube.exe' -Uri 'https://github.com/kubernetes/minikube/releases/latest/download/minikube-windows-amd64.exe' -UseBasicParsing
```

executer la commande suivante pour ajouter minikube à votre ClassPath

```bash
$oldPath = [Environment]::GetEnvironmentVariable('Path', [EnvironmentVariableTarget]::Machine)
if ($oldPath.Split(';') -inotcontains 'C:\minikube'){ `
  [Environment]::SetEnvironmentVariable('Path', $('{0};C:\minikube' -f $oldPath), [EnvironmentVariableTarget]::Machine) `
}
```

On peut maintenant lancer minikube avec la commande

```bash
minikube start
```

# utilisation de CLI

Voici les differentes commandes de base de cube control

```bash
kubectl run
kubectl create
kubectl apply
```

Pour connaitre la version de kubernetes

```bash
kubectl version
```

On peut maintenant lancer notre permiere commande kubernetes pour creer un pod

```bash
kubectl run my-nginx --image nginx
```

On peut afficher la liste des pods dans kubernetes

```bash
kubectl get pods
```

Ou afficher tous les objets existants dans kubernetes

```bash
kubectl get all
```

On pourra supprimer l objet deployement avec la commande

```bash
kubectl delete deployment my-nginx
```

# ReplicaSet

On peut ajouter des replicas aux pods

voici un exemple de replicaSet

creation d'un pod apache

```bash
kubectl run my-apache --image httpd
```

affiche le pod nouvellement cree

```bash
kubectl get all
```

Creation du replica avec l'une des deux commandes suivantes

```bash
kubectl scale deploy/my-apache --replicas 2
kubectl scale deployment my-apache --replicas 2
```

# Objet Kubernetes

On peut afficher differentes informations sur nos objets kubernetes

La commande suivante permet d afficher la liste des differents pods qui existe dans kubernetes

```bash
kubectl get pods
```

On peut afficher les logs d'un objet grace a

```bash
kubectl logs deployment/my-apache [--follow --tail l]
```

ou directement lors de la creation du pods

```bash
kubectl logs -l run=my-apache
```

ou utiliser la commande describe pour avoir plus d information sur l objet

```bash
kubectl describe pod/my-appache-xxxx-yyyy
```

on peut afficher les informations de l'objet en temps reel grace à l'option watch

```bash
kubectl get pods -w
```

Dans une autre fenetre lancer la commande

```bash
kubectl delete pod/my-apache-xxxx-yyyy
```

dans la premier fenetre on peut voir la recreation du pod lancer par kubernetes pour respecter les specifications

On pourra supprimer notre deployement avec

```bash
kubectl delete deployment my-apache
```

# service kuberentes

on peut executer un service grace a la commande expose

```bash
kubectl expose
```

## Creation d'un service ClusterIP

Ouvrir deux fenetres de terminal

lancer la commande pour voir l etats des pods lors de la creation du service ClusterIP

```bash
kubectl get pods -w
```

Dans la deuxieme fenetre de terminal lancer la commande pour creer le pod à exposer

```bash
kubectl create deployment httpenv --image=bretfisher/httpenv
```

On ajoute 5 replicas au pod

```bash
kubectl scale deployment/httpenv --replicas=5
```

Creer le service qui exposera le pod

```bash
kubectl expose deployment/httpenv --port 8888
```

Vérifier les informations du service nouvellement créé

```bash
kubectl get service
```

Creer un autre pour tester l'accessibilite au service

```bash
kubectl create pod tmp-shell --image alpine
```

Lancer la commande pour tester la connexion

```bash
kubectl exec -it tmp-shell -- curl httpenv:8888
```

Voici la sortie de la commande

```json
{
  "HOME": "/root",
  "HOSTNAME": "httpenv-6fdc8554fb-nz4bz",
  "KUBERNETES_PORT": "tcp://10.96.0.1:443",
  "KUBERNETES_PORT_443_TCP": "tcp://10.96.0.1:443",
  "KUBERNETES_PORT_443_TCP_ADDR": "10.96.0.1",
  "KUBERNETES_PORT_443_TCP_PORT": "443",
  "KUBERNETES_PORT_443_TCP_PROTO": "tcp",
  "KUBERNETES_SERVICE_HOST": "10.96.0.1",
  "KUBERNETES_SERVICE_PORT": "443",
  "KUBERNETES_SERVICE_PORT_HTTPS": "443",
  "PATH": "/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin"
}
```

## Creation d'un service NodePort

Verifier le status de nos services avant de la creation du NodePort

```bash
kubectl get service
```

Puis lancer la commande pour creer le nouveau service

```bash
kubectl expose deployment/httpenv --port 8888 --name httpenv-np --type NodePort
```

Vérifier les informations du service nouvellement créé

```bash
kubectl get service
```

## Creation d'un service LoadBalancer

Verifier le status de nos services avant de la creation du LoadBalancer

```bash
kubectl get service
```

Puis lancer la commande pour creer le nouveau service

```bash
kubectl expose deployment/httpenv --port 8888 --name httpenv-lb --type LoadBalancer
```

Vérifier les informations du service nouvellement créé

```bash
kubectl get service
```

le service peut etre contacter avec la commande ou via un fureteur

```bash
curl localhost:8888
```

Supprimer les services et deploiements pour remettre la stack d'objet à zero

```bash
kubectl delete service/httpenv service/httpenv-np
kubectl delete service/httpenv-lb deployment/httpenv
```

## service DNS

Les namespaces et les noms des pods de kubernetes peuvent etre utiliser
pour faire la resolution DNS.

Retrouver la liste des namespaces des elements kubernetes avec la commande

```bash
kubectl get namespaces
```

![FQDN](assets/FQDN.jfif)

les services ont egalement un FQDN

```bash
curl <hostname>.<namespace>.svc.cluster.local
```

# Generator

```bash
kubectl create deployment sample --image nginx --dry-run -o yaml
kubectl create deployment test --image nginx --dry-run -o yaml
kubectl create job test --image nginx --dry-run -o yaml
kubectl expose deployment/test --port 80 --dry-run -o yaml
```

## run confusion

```bash
kubectl run test --image nginx --dry-run
kubectl run test --image nginx --port 80 --expose --dry-run
kubectl run test --restart OnFailure --dry-run
kubectl run test --restart Never --dry-run
kubectl run test --schedule “*/1 * * *” --dry-run
```

# gestion de kubernetes

```bash
kubectl apply -f my-resources.yaml
```

# YAML file

```bash
kubectl api-resources
```

```bash
kubectl api-versions
```

```bash
kubectl explain service --recursive
kubectl explain service.spec
kubectl explain service.spec.type
```

# Monitoring

```bash
kubectl create -f prometheus/prometheus.yml
kubectl apply -f k8s-monitoring/monitoring.yml
```

# Pratique

## database

creation d'une base de donnee postgre

```bash
docker build -t fullstackappdb .
kubectl exec -it postgres -c postgres -- psql -U postgres
```

## backend

creation d'un service REST avec python

```bash
docker build -t backend .
```

## frontend

creation d'un frontend ReactJS

```bash
docker build -t frontend .
```
