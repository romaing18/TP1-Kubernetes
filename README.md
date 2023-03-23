
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