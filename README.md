# SPEKT<img width="30" alt="portfolio_view" src="https://github.com/spekt8/spekt8/blob/master/dist/images/src/client/images/spekt8-logo-ecbc06d5.png">

SPEKT8 is a new visualization tool for your Kubernetes clusters. It automatically builds logical topologies of your application and infrastructure, which enable your SRE and Ops team to intuitively understand, monitor, and control your containerized, microservices based application. Simply deploy our containerized application directly into your Kubernetes cluster. 

# spekt8

## Le namespace

Si vous n'avez pas encore de namespace pour installer spekt8, en créer un :

```
kubectl create namespace poc-bruno
```

Vérifier qu'il a bien été créé :

```
kubectl get namespaces
```

## Le contexte

Si vous n'avez pas encore de contexte, en créer un :

```
kubectl config set-context poc-bruno-spekt8 --cluster=kubernetes --user=kubernetes-admin --namespace=poc-bruno
kubectl config use-context poc-bruno-spekt8
```

Vérifier le contexte actif :

```
kubectl config get-contexts
```

Vérifier qu'on est connecté :

```
kubectl cluster-info
```

### Cas où il manque des hôtes dans le fichier /etc/hosts

Au besoin, dans le fichier /etc/hosts, ajouter la ligne suivante :

```
192.168.122.72 intr-devops-pockube-vm2
```

## Le déploiement

Modifier éventuellement le déploiement :

```
cat spekt8-deployment.yaml
```

```
cat fabric8-rbac.yaml
```

## Ajout de Kustomize

Créer un fichier kustomization.yaml :

```
touch kustomization.yaml
```

Corrige le fichier pour initialiser son contenu :

```
kustomize edit fix
```

Ajouter les fichiers deux ressources de spekt8 :

```
kustomize edit add resource spekt8-deployment.yaml fabric8-rbac.yaml
```

Afficher le résultat :

```
kustomize build
```

Dans une fenêtre, afficher la liste des pods :

```
watch kubectl -n poc-bruno get pods -l component=spekt8
```


Dans une autre fenêtre, afficher les logs :

```
stern -n poc-bruno -l component=spekt8
```

Appliquer la ressource Kustomize que l'on vient de créer :

```
kubectl apply -k .
```

Afficher la liste des pods :

```
kubectl get pods
```

Afficher la liste des services :

```
kubectl get svc
```

## Créer un service

```
vi spekt8-service.yaml
```

Y ajouter ce contenu :

```
apiVersion: v1
kind: Service
metadata:
  name: spekt8
  namespace: poc-bruno
spec:
  type: NodePort
  selector:
    component: spekt8
  ports:
    - port: 3000
      protocol: TCP
      nodePort: 30432
```

Ajouter ce nouveau fichier à Kustomize :

```
kustomize edit add resource spekt8-service.yaml
```

Appliquer la ressource Kustomize :

```
kubectl apply -k .
```

Pour information, cette ligne équivaut à :

```
kustomize build | kubectl apply -f -
```

Afficher la liste des services :

```
kubectl get svc
```

Vérifier que le service est accessible depuis la machine :

```
curl http://intr-devops-pockube-vm2:3000
```

Hors de la machine, dans un navigateur, aller à l'adresse :

```
http://intr-devops-pockube-vm2:30432
```

Pour tout désinstaller :

```
kubectl delete -k .
```






<img src="https://github.com/spekt8/spekt8/blob/master/spekt8-preview.gif" width="600" height="300" />

When your app is running in Kubernetes, our visualization tool will display all the Pods, Services, and Ingresses that allow you to drill down on Kubernetes clusters. The information provided to you includes but is not limited to the following:
* Indicates the status of pods (blue for running, yellow for pending)
* Lists the specific container images running within pods
* Provides information on ingresses and services (Nodeport, load balancer, cluster-ip, persistent volume claims, persistent volumes, etc.)

While the current app does not directly visualize any controllers (such as Deployments, Daemon Sets, Replication Controllers) it does indirectly visualize them through their pods. We are continuously building more support for additional Kubernetes objects.

In addition to these views, nodes can be presented either in graphical or in table mode. The graphical mode is practical for obtaining a quick visual overview of your app, and its infrastructure and connections between all of the nodes. And when you switch to table mode, nodes are presented in a convenient list that displays the resources being consumed by processes, containers, and hosts.

## Deployment
These instructions presume you have a Kubernetes cluster already running. 

An image of the application has been pushed to [Docker Hub](https://hub.docker.com/r/elliotxkim/spekt8/tags/) for those who would like to build the image directly from the public repository. 

We are also providing a deployment file that you can apply directly to your Kubernetes cluster using the command line. 

* kubectl apply -f [spekt8-deployment.yaml](https://raw.githubusercontent.com/spekt8/spekt8/master/spekt8-deployment.yaml)
* kubectl port-forward deployment/spekt8 3000:3000

In addition, in order to allow reading resources of the API, you must configure a set of permissions. We have set up a YAML file using RBAC authorization which you can apply directly to your Kubernetes cluster using the command line:
* kubectl apply -f [fabric8-rbac.yaml](https://raw.githubusercontent.com/spekt8/spekt8/master/fabric8-rbac.yaml).

Then, open your web browser to [http://localhost:3000](http://localhost:3000).


## How to Use 

**Navigation**
* Use arrow keys for movement
* Use the ‘-’ or ‘+’ for zoom

**Legend**
* Use the switch icons next to the legend to filter based on Object Types

**Graph**
* Click on Pods/Services/Ingresses to see more detailed information about the selected Object

**View**
* Use the View dropdown to toggle between Graph and Table Views

## Built With
* [React](https://github.com/facebook/react) 
* [Redux](https://github.com/reduxjs/redux) 
* [React-Graph-Vis](https://github.com/crubier/react-graph-vis)
* [Jest](https://github.com/facebook/jest/)


## Authors
* **[Elliot Kim](https://www.linkedin.com/in/elliotjykim/)** - [@elliotxkim](https://github.com/elliotxkim)
* **[Edward Roh](https://www/linkedin.com/in/edwardroh)** - [@the3ddy](https://github.com/the3ddy)
* **[Camille Lambert](https://www.linkedin.com/in/camillelambert/)** - [@syntheticproduct](https://github.com/syntheticproduct)
* **[Miles Cole](https://www.linkedin.com/in/colemiles/)** - [@milescole](https://github.com/milescole)


## License

This project is licensed under the MIT License - see the [LICENSE.md](https://github.com/spekt8/spekt8/blob/master/LICENSE) file for details
