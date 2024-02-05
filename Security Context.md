## Security Context

### Task 1: Set the security context for a pod
```
vi sc-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: sc-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  volumes:
    - name: sc-vol
      emptyDir: {}
  containers:
    - name: sc-ctr
      image: busybox
      command:
        - sh
        - -c
        - sleep 1h
      volumeMounts:
        - name: sc-vol
          mountPath: /data/demo
      securityContext:
        allowPrivilegeEscalation: false
```
```
kubectl create -f sc-pod.yaml
```
```
kubectl exec -it sc-pod -- sh
```
```
ps
```
```
cd /data
```
```
ls
```
```
cd demo
```
```
echo hello > testfile
```
```
ls -l
```
```
id
```

### Task 2: Set the security context for a container

```
vi sc-ctr.yaml
```
```yaml

apiVersion: v1
kind: Pod
metadata:
  name: sc-pod2
spec:
  containers:
  - name: sc-ctr2
    image: gcr.io/google-samples/node-hello:1.0
    securityContext:
      runAsUser: 2000
      allowPrivilegeEscalation: false  
```
```
kubectl create -f sc-ctr.yaml
```
```
kubectl get pods
```
```
kubectl exec -it sc-pod2 -- sh
```
```
ps aux
```
```
exit
```
