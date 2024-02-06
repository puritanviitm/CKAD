## Security Context

### Task 1: Set the security context for a Pod

To specify security settings for a Pod, include the securityContext field in the Pod specification. The securityContext field is a PodSecurityContext object. The security settings that you specify for a Pod apply to all Containers in the Pod. Here is a configuration file for a Pod that has a securityContext and an emptyDir volume:

```
vi security-context.yaml
```
```yaml

apiVersion: v1
kind: Pod
metadata:
  name: security-context-pod
spec:
  securityContext:
    runAsUser: 1000
    runAsGroup: 3000
    fsGroup: 2000
  containers:
  - name: sec-ctx-demo
    image: busybox:1.28
    command: [ "sh", "-c", "sleep 1h" ]

```
Create the Pod:
```
kubectl apply -f security-context.yaml
```
Verify that the Pod's Container is running:
```
kubectl get pod security-context-pod
```
Get a shell to the running Container:
```
kubectl exec -it security-context-pod -- sh
```
In your shell, list the running processes:
```
ps
```
The output shows that the processes are running as user 1000, which is the value of runAsUser:

In your shell, navigate to /data, and list the one directory:
```
cd /data && ls -l
```
The output shows that the /data/demo directory has group ID 2000, which is the value of fsGroup.

In your shell, navigate to /data/demo, and create a file:
```
cd demo && echo hello > testfile
```
List the file in the /data/demo directory:
```
ls -l
```
The output shows that testfile has group ID 2000, which is the value of fsGroup.

Run the following command:
```
id
```

From the output, you can see that gid is 3000 which is same as the runAsGroup field. If the runAsGroup was omitted, the gid would remain as 0 (root) and the process will be able to interact with files that are owned by the root(0) group and groups that have the required group permissions for the root (0) group.
```
exit
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
