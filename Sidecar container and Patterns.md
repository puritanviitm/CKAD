## Sidecar container and Patterns

### Task 1: Sidecar container
```
vi sidecar.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sidecar-pod
spec:
  containers:
  - name: main-container
    image: nginx:latest
    ports:
    - containerPort: 80
    # Main application container

  - name: sidecar-container
    image: busybox:latest
    command: ['sh', '-c', 'while true; do echo "Sidecar Running"; sleep 10; done']
    # Sidecar container

```
```	
kubectl apply -f sidecar.yaml
```
```
kubectl get pod
```
```
kubectl exec -it sidecar-pod -c sidecar-container -- sh
```
```
install curl
```
```
apt update && apt install curl -y
```
``` 
curl 'http://localhost:80/app.txt'
```
