## Node Labelling and Constraining pods in Kubernetes

### Task 1: Deploy pod in specific node based on node-name / hostname
```
vi node-name.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-nodename
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: nginx
    imagePullPolicy: IfNotPresent
  nodeName: {node-name / hostname }
```
```
kubectl create -f node-name.yaml
```
```
kubectl get pods -o wide 
```

Check your pod is running on the targeted node
 
### Task 2: Node labeling and constraining pods

list node labels
```
kubectl get nodes --show-labels
```
```
kubectl label nodes <node_name> disktype=ssd
```
List you nodes to check labels 
```
kubectl get nodes --show-labels | grep "disktype=ssd"
```
```
vi nlns-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nlns-nginx-pod
  labels:
    env: test
spec:
  containers:
  - name: nlns-nginx-ctr
    image: nginx
  nodeSelector:
    disktype: ssd

```
```
kubectl apply -f nlns-pod.yaml
```
```
kubectl get pods -o wide
```
