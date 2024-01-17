## Sidecar container and Patterns

### Task 1: Sidecar container
```
vi sidecar.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-sidecar
spec:
  containers:
  - name: app-container
    image: ubuntu:latest
    command: ["/bin/sh"]
    args: ["-c","while true; do date >> /var/log/app.txt; sleep 5; done"]
    volumeMounts:
    - name: share-logs
      mountPath: /var/log/
  - name: sidecar-container
    image: nginx:latest
    ports:
    - containerPort: 80
    volumeMounts:
    - name: share-logs
      mountPath: /usr/share/nginx/html
  volumes:
  - name: share-logs
    emptyDir: {}
```
```	
kubectl create -f sidecar.yaml
```
```
kubectl get pod
```
```
kubectl exec -it pod-sidecar -c sidecar-container -- bash
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
