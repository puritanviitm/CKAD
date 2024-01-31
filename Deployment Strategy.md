## Deployment Strategy

### Task 1: Canary Deployment in Kubernetes 
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
      type: web-app
  strategy:
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: web-blue
        type: web-app
    spec:
      containers:
      - image: mandarct/web-blue:v1
        name: web-blue
        ports:
        - containerPort: 80
          protocol: TCP
```
		 
kubectl create -f web-blue.yaml
kubectl get deploy
kubectl get pods

Now create NodePort service to access application

		 
vi svc-web-lb.yaml

apiVersion: v1
kind: Service
metadata:
  name: web-app-svc-lb
spec:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    type: web-app
  type: NodePort
  ports:
   - port: 80
     targetPort: 80
	 
kubectl create -f svc-web-lb.yaml
kubectl get svc

access you application


to it canary deployment deploy following yaml file

vi web-green.yaml

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
        type: web-app
    spec:
      containers:
      - image: mandarct/web-green:v1
        name: web-green
        ports:
        - containerPort: 80
          protocol: TCP

kubectl create -f web-green.yaml
kubectl get deployment
kubectl get pods

access this application using same service that we create previously.

some time will to blue deployment web page, some time green webpage

If you delete the web-green deployment, load-balancer will start sending traffic only to the blue pods


kubectl describe svc web-app-svc-lb
