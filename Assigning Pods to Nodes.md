## Assigning Pods to Nodes

### Task 1: Node labels

Node Labels: Like many other Kubernetes objects, nodes have labels. You can attach labels manually. Kubernetes also populates a standard set of labels on all nodes in a cluster.

Node Selector: Node Selector is the simplest recommended form of node selection constraint. You can add the `nodeSelector` field to your Pod specification and specify the node labels you want the target node to have. Kubernetes only schedules the Pod onto nodes that have each of the labels you specify.

List node labels
```
kubectl get nodes --show-labels
```
```
kubectl label nodes <node_name> disktype=ssd
```
List your nodes to check labels 
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

### Task 2: Deploy pod in specific node based on node-name / hostname

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
 

