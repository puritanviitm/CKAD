## Resource Quotas

### Task 1: Creating a Namespace
```
kubectl create namespace quotas
```
```
kubectl get ns
```
```
kubectl describe ns quotas
```


### Task 2: Creating a resourcequota
Create a pod to before applying resource quota to check if the resource quota is applicable to existing object or not.
```
kubectl -n quotas run quota-pod --image nginx --port 80
```
```
vi quota.yaml
```
```yaml

apiVersion: v1
kind: ResourceQuota
metadata:
  name: quota
  namespace: quotas
spec:
  hard:
    requests.cpu: "1"
    requests.memory: 1Gi
    limits.cpu: "2"
    limits.memory: 2Gi

```
```
kubectl apply -f quota.yaml
```
```
kubectl describe ns resourcequota
```


### Task 3: Verify Resource Quota Functionality
```
vi rq-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: quota-pod
  namespace: quotas
spec:
  containers:
  - name: quota-ctr
    image: nginx
    resources:
      limits:
        memory: "800Mi"
        cpu: "1000m"
      requests:
        memory: "600Mi"
        cpu: "350m"
    ports:
      - containerPort: 80
```
```	  
kubectl create -f rq-pod.yaml
```
```
kubectl describe ns resourcequota
```
```
kubectl get resourcequota -n quotas -o yaml
```

### Task 4: Limiting Number of Pods
```
kubectl edit resourcequotas quota -n quotas
```
add following lines in spec: > hard:
counts/deployments.apps: 2
counts/replicasets.apps: 3


### Task 5: Clean-up

#Delete the quota to clean up.
```
kubectl delete ns quotas
```
