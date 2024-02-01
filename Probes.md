## Probes

### Task 1: Liveness probes
```	  
vi liveness-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    app: lns
  name: liveness-pod
spec:
  containers:
  - name: liveness
    image: busybox
    args:
    - /bin/sh
    - -c
    - touch /liveness; sleep 6000
    livenessProbe:
      exec:
        command:
        - cat
        - /liveness
      initialDelaySeconds: 5
      periodSeconds: 5
```
```	  
kubectl get pods	  
```
```
kubectl describe pod liveness-exec
``` 
Now login to the container and delete the file
```
kubectl exec -it liveness-pod sh 
```
```
rm -f /liveness
```
```
exit
```
```
kubectl get pods
```
You can see the pod is getting restarted

 
Task 2: Readiness probe
------------------------------------------ 

vi readiness.yaml

apiVersion: v1
kind: Pod
metadata:
  labels:
    app: rns
  name: readiness-pod
spec:
  containers:
  - name: readiness
    image: nginx
    args:
    - /bin/bash
    - -c
    - service nginx start; touch /readiness; sleep 6000
    readinessProbe:
      exec:
        command:
        - cat
        - /readiness
      initialDelaySeconds: 5
      periodSeconds: 5
	  
kubectl create -f readiness.yaml
kubectl get pods


login inside pod and delete folloing file 

kubectl exec -it readiness-pod bash 
# rm -f /readiness
# exit


View the Pod events and see that readiness probe has failed
kubectl describe pods readiness-pod

now describe service
kubectl describe svc readiness-svc

End point is gone
