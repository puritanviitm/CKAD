## Deployment Strategy

### Task 1: Recreate Strategy in Kubernetes 
```
vi recreate.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: dep1
  name: dep1
spec:
  replicas: 2
  selector:
    matchLabels:
      app: dep1
  strategy:
    type: Recreate
  template:
    metadata:
      labels:
        app: dep1
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
```
```
kubectl apply -f recreate.yaml
```
Add a watch on the pods in a new tab
```
kubectl get po -w
```
Set a new image for the deployment
```
kubectl set image deploy dep1 nginx=nginx:latest --record
```
Check how the pods are getting deleted and recreated. 

Cross check if the image has been updated by executing the below command
```
kubectl describe deployments.apps dep1
```

### Task 2: Blue/Green Deployment in Kubernetes 
```
vi web-blue.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-blue
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-blue
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web-blue
    spec:
      containers:
      - image: mandarct/web-blue:v1
        name: web-blue
        ports:
        - containerPort: 80
          protocol: TCP
```
```		 
kubectl apply -f web-blue.yaml
```
```
kubectl get deploy
```
```
kubectl get pods
```

Now create NodePort service to access application
```		 
vi svc-web.yaml
```
```yaml
apiVersion: v1
kind: Service
metadata:
  name: svc-web
spec:
  ports:
  - port: 80        # Service port(internal)
    protocol: TCP
    targetPort: 80  # Container port
    nodePort: 32123 # Range from 30000 to 32767 . This is the external port number
  selector:
    app: web-blue
  type: NodePort

```
```	 
kubectl apply -f svc-web.yaml
```
```
kubectl get svc
```
```
kubectl get po -o wide
```
Check the endpoints
```
kubectl get ep svc svc-web
```
Access you application

Create another deployment (Green)
```
vi web-green.yaml
```
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-green
spec:
  replicas: 3
  selector:
    matchLabels:
      app: web-green
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web-green
    spec:
      containers:
      - image: mandarct/web-green:v1
        name: web-green
        ports:
        - containerPort: 80
          protocol: TCP
```
```
kubectl apply -f web-green.yaml
```
```
kubectl get deployment
```
```
kubectl get pods
```
Access this application using same service that we created previously, by changing the selector in the Service yaml file.

Replace Selector `web-blue` by `web-green`
```	 
kubectl apply -f svc-web.yaml
```
```
kubectl get svc
```
```
kubectl get po -o wide
```
Check the endpoints
```
kubectl get ep svc svc-web
```
Access you application on the port 32123

### Task 3: Canary Deployment in Kubernetes 

Service and deployment should have a common label.
Add `type: web-app` to yaml file of both the deployments and apply again.
```
kubectl apply -f web-green.yaml
```
```
kubectl apply -f web-blue.yaml
```
In the yaml file of Service change the Selector to 'type: web-app` and apply.

Check the endpoints of the service. It should show all the pods of both the deployments.
```
kubectl get ep svc svc-web
```
```
kubectl get po -o wide
```
Access the application on port 32123. Sometime it will show Blue, other time it will show green.




