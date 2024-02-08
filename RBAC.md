## RBAC

RBAC stands for Role-Based Access Control. 

It is a method of regulating access to resources based on the roles of individual users within an organization. With RBAC, you can specify what actions (such as create, read, update, delete) a user or group of users can perform on specific Kubernetes resources.

### Task 1: Create a new ServiceAccount
```
kubectl get ns
```
```
kubectl create ns purple

```
```
kubectl create sa -n purple demo-sa 
```
```
kubectl get ns
```

### Task 2: Create a new Role and RoleBinding 
```
kubectl create role demo-role --verb=list --resource=pods -n purple
```
```
kubectl create rolebinding demo-rb --role=demo-role --serviceaccount=purple:demo-sa -n purple
```

### Task 3: Test whether you are able to do a GET request to Kubernetes API 
```
kubectl run test --image=nginx -n purple
```
```
kubectl exec -it -n purple test -- /bin/bash 
```
```
curl -v --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default/api/v1/namespaces/purple/pods 
```
```
vi pod-sa.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-01
  namespace: purple
spec:
  serviceAccountName: demo-sa
  containers:
  - name: my-container
    image: nginx
```
```
kubectl apply -f pod-sa.yaml
```
```
kubectl exec -it -n purple test-01 -- /bin/bash 
```
```
curl -v --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default/api/v1/namespaces/purple/pods 
```

