## Resource Quotas

### Task 1: Creating a Namespace

In Kubernetes, a ResourceQuota is a way to limit the amount of resources (CPU, memory, and persistent storage) that a namespace can consume. It helps in preventing resource exhaustion and ensures that one namespace doesn't negatively impact the performance or stability of others.

Keep in mind that ResourceQuotas are a way to define constraints, and they do not actively enforce them on existing resources. They are checked when new resources are created or when existing resources are updated. If a namespace exceeds its quota, the creation or update of resources may be rejected.

The below command can identify all namespaced objects
```
kubectl api-resources
```
```
kubectl create namespace quotas
```
```
kubectl get ns
```
```
kubectl describe ns quotas
```


### Task 2: Creating a Resource Quota

Create a pod and expose it, before applying the resource quota to check if the resource quota applies to existing objects or not.
```
kubectl -n quotas run pod1 --image nginx --port 80
```
```
kubectl -n quotas expose pod pod1 --name pod1-svc --port 80 --type NodePort
```
```
kubectl -n quotas run pod2 --image nginx --port 80
```
```
kubectl -n quotas expose pod pod2 --name pod2-svc --port 80 --type NodePort
```
Imperative 
```
kubectl -n quotas create quota rs-quota1 --hard=pods=3,services=3
```
Declarative
```
vi quota.yaml
```
```yaml

apiVersion: v1
kind: ResourceQuota
metadata:
  name: rs-quota2
  namespace: quotas
spec:
  hard:
    pods: "2"
    services: "2"

```
```
kubectl apply -f quota.yaml
```
```
kubectl describe ns quotas
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
