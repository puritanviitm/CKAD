## ConfigMap & Secrets

### Task 1: Directly inject variables-Traditional Method
```
vi env.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: ws
  name: env-pod
spec:
  containers:
  - image: nginx
    name: ng-ctr
    ports:
    - containerPort: 80
    env:
    - name: db_user   #key
      value: admin
    - name: db_pwd
      value: "1234"
```
```
kubectl apply -f env.yaml
```
```
kubectl describe pod env-pod
```
Enter the pod and check if the variable has been passed correctly or not
```
kubectl exec -it env-pod -- sh
```
```
echo $db_user
```
```
echo $db_pwd
```
```
env | grep db_
```

### Task 2: Inject varialbes using ConfigMaps - FromLiteral
Create a ConfigMap
```
kubectl create cm cm-1 --from-literal=db_user=admin --from-literal=db_pwd=1234
```
```
kubectl get cm
```
```
kubectl describe cm cm-1
```
Inject the ConfigMap into the Pod Yaml File
```
vi env.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: web
  name: web-pod
spec:
  containers:
  - image: httpd
    name: ctr-1
    ports:
    - containerPort: 80
    envFrom:
    - configMapRef:
        name: cm-1
```
```
kubectl apply -f env.yaml
```
```
kubectl describe pod web-pod
```
Enter the pod and check if the variable has been passed correctly or not
```
kubectl exec -it web-pod -- sh
```
```
echo $db_user
```
```
echo $db_pwd
```
```
env | grep db_
```


```
vi pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    app: web
  name: web-pod2
spec:
  containers:
  - image: httpd
    name: ctr-1
    ports:
    - containerPort: 80
    env:
    - name: db_password
      valueFrom:
        configMapKeyRef:
          name: cm-1
          key: db_pwd
----------------------------------------------------------------------
vi pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    app: web
  name: web-pod3
spec:
  volumes:
  - name: cm-volume
    configMap:
      name: cm-2
  containers:
  - image: httpd
    name: ctr-1
    volumeMounts:
    - name: cm-volume
      mountPath: /app
    ports:
    - containerPort: 80
-----------------------------------------------------------------


kubectl create secret generic secret-1 --from-literal=db_user=admin --from-literal=db_pwd=123
kubectl get secret
kubectl describe secret secret-1
----------------------------------------------------------------------------------
vi sc-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: sc-pod
  name: sc-pod
spec:
  containers:
  - image: nginx
    name: sc-ctr
    envFrom:
    - secretRef:
        name: secret-1
 ---------------------------------------------------------------------------------

  vi sc-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    run: sc-pod
  name: sc-pod
spec:
  containers:
  - image: nginx
    name: sc-ctr
    env:
    - name: password  #new key
      valueFrom:
        secretKeyRef:
          name: secret-1
          key: db_pwd
 -------------------------------------------------------------------------------------
 vi pod.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    app: web
  name: sc-pod
spec:
  volumes:
  - name: sc-volume
    secret:
      secretName: secret-1
  containers:
  - image: httpd
    name: ctr-1
    volumeMounts:
    - name: sc-volume
      mountPath: /app
    ports:
    - containerPort: 80
