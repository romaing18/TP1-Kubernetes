
# TP01 : Introduction à Kubernetes
## Pod Nginx
### Déployer un pod nginx
La commande qui permet de pouvoir déployer un pod nginx est :
```
kubectl run my-pod-nginx --image=nginx
```
Résultat :
```
pod/my-pod-nginx created
```
### A l’aide de la commande kubectl port-forward et d’un navigateur accéder à la page par défaut de votre pod Nginx
```
kubectl port-forward my-pod-nginx 80:8080
```
Résultat :
````
Forwarding from 127.0.0.1:8080 -> 8080
Forwarding from [::1]:8080 -> 8080
````
## Connexion entre plusieurs Pods
### A l’image du TP 1 sur Docker (question 7 et 8), héberger un Pod phpmyadmin et mysql, cette fois-ci en utilisant minikube
#### Partie phpmyadmin
Fichier configuration de phpmyadmin:
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: phpmyadmin-deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: phpmyadmin
  template:
    metadata:
      labels:
        app: phpmyadmin
    spec:
      containers:
        - name: phpmyadmin
          image: phpmyadmin/phpmyadmin
          ports:
            - containerPort: 80
          env:
            - name: PMA_HOST
              value: mysql
            - name: MYSQL_ROOT_PASSWORD
              value: P@ssword
            - name: PMA_PORT
              value: "3306"
```
Apply la configuration de phpmyadmin :
```
kubectl apply -f conf-phpmyadmin.yml
```
#### Partie MySQL
Fichier de configuration de MySQL :
```
---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: mysql
spec:
  selector:
    matchLabels:
      app: mysql
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: mysql
    spec:
      containers:
      - image: mysql:5.6
        name: mysql
        env:
        - name: MYSQL_ROOT_PASSWORD
          value: P@ssword
        ports:
        - containerPort: 3306
          name: mysql
        volumeMounts:
        - name: mysql-persistent-storage
          mountPath: /var/lib/mysql
      volumes:
      - name: mysql-persistent-storage
```
Apply la configuration de mysql :
```
kubectl apply -f conf-mysql.yml
```
### Créer un service associé au Pod mysql
Voici le service associé au pod MySQL :
```
---
apiVersion: v1
kind: Service
metadata:
  name: mysql
spec:
  ports:
  - port: 3306
  selector:
    app: mysql
```
### Connecter phpmyadmin avec le Service mysql et vérifier que vous pouvez administrer cette base de données
Voici le service qui permet de connecter phpmyadmin avec le service MySQL :
```
---
apiVersion: v1
kind: Service
metadata:
  name: phpmyadmin-service
spec:
  type: NodePort
  selector:
    app: phpmyadmin
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```
### Avec la commande kubectl-port forward, vérifier que phpmyadmin arrive à contacter et administrer votre base de données mysql
Commande port-forward sur le port 8080 du fichier conf de phpmyadmin pour pouvoir acceder à l’interface graphique :
```
kubectl port-forward phpmyadmin-deployment-54f84d8f8f-488qf 8080:80
Forwarding from 127.0.0.1:8080 -> 80
Forwarding from [::1]:8080 -> 80
Handling connection for 8080
```