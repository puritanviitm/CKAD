## Pod Scheduling

### Task 1: Pod Scheduling using Node labels

Node Labels: Like many other Kubernetes objects, nodes have labels. You can attach labels manually. Kubernetes also populates a standard set of labels on all nodes in a cluster.

Node Selector: Node Selector is the simplest recommended form of node selection constraint. You can add the `nodeSelector` field to your Pod specification and specify the node labels you want the target node to have. Kubernetes only schedules the Pod onto nodes that have each of the labels you specify.

List node labels
```
kubectl get nodes --show-labels
```
Label the node
```
kubectl label nodes node1 disktype=ssd1
```
List your nodes to check labels 
```
kubectl get nodes --show-labels | grep "disktype=ssd1"
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
The Pod goes into the pending state as the node selector does not match.

Either change the Label on the Node or the Node Selector specifications in the pod.

Changing the Label on the node. For this Unlabel the Node and mark it with the correct label
```
kubectl label nodes node1 disktype-
```
```
kubectl label nodes node1 disktype=ssd
```
List your nodes to check labels 
```
kubectl get nodes --show-labels | grep "disktype=ssd"
```
List the pods. You will see the the Pod has gone into the running state.
```
kubectl get po -o wide
```


### Task 2: Pod Scheduling using Node Name / Host Name

Node Name is a more direct form of node selection than affinity or nodeSelector. nodeName is a field in the Pod spec. If the nodeName field is not empty, the scheduler ignores the Pod and the kubelet on the named node tries to place the Pod on that node. Using nodeName overrules using nodeSelector or affinity and anti-affinity rules.

Some of the limitations of using nodeName to select nodes are:

* If the named node does not exist, the Pod will not run, and in some cases may be automatically deleted.
* If the named node does not have the resources to accommodate the Pod, the Pod will fail and its reason will indicate why, for example OutOfmemory or OutOfcpu.
* Node names in cloud environments are not always predictable or stable.

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
  nodeName: {node-name / hostname }
```
```
kubectl apply -f node-name.yaml
```
```
kubectl get pods -o wide 
```
Check your pod is running on the targeted node


### Task 3: Pod Scheduling using Node Affinity
```
vi node-aff-pref.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: na-nginx-pod1
spec:
  containers:
  - name: na-nginx-ctr1
    image: nginx
  affinity:
    nodeAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1
        preference:
          matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd
```
```
kubectl apply -f node-aff-pref.yaml
```
```
kubectl get pods -o wide
```
Remove node label 
```
kubectl label nodes <node_name> disktype-
```
Deploy same pod again it is getting deployed

Task 1.2: required during scheduling

remove label first 
kubectl label nodes <node_name> disktype-


vi aff-na-pod2.yaml

apiVersion: v1
kind: Pod
metadata:
  name: na-nginx-pod2
spec:
  containers:
  - name: na-nginx-ctr2
    image: nginx
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: disktype
            operator: In
            values:
            - ssd

kubectl create -f aff-na-pod2.yaml

pod is strictly looking for labeled node

kubectl get pods -o wide
kubectl label nodes <node_name> disktype=ssd

once label is added pod will deployed


### Task 4: Pod Scheduling using Pod Affinity


vi depend-pod.yaml

apiVersion: v1
kind: Pod
metadata:
  name: pa-nginx-pod1
  labels:
    app: pa-nginx-app
spec:
  containers:
  - name: pa-nginx-ctr1
    image: nginx

kubectl create -f depend-pod.yaml
kubectl get pods

---------------------------------------------------------------------
Task 2.1: create pod with pod affinity
---------------------------------------------------------------------
vi aff-pa-pod2.yaml

apiVersion: v1
kind: Pod
metadata:
  name: pa-nginx-pod2
spec:
  containers:
  - name: pa-nginx-ctr2
    image: nginx
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: app
            operator: In
            values:
            - pa-nginx-app
        topologyKey: kubernetes.io/hostname

kubectl create -f pod-affinity.yaml

kubectl get pods -o wide
both pod are in same node

---------------------------------------------------------------------
Task 2.3: pod anti affinity
---------------------------------------------------------------------

vi pod-antiaff.yaml

apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  selector:
    matchLabels:
      app: nginx
  replicas: 1
  template:
    metadata:
      labels:
        app: nginx
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - pa-nginx-app
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: nginx
        image: nginx

kubectl create -f  pod-antiaff.yaml
kubectl get pod -o wide 


kubectl delete -f  pod-antiaff.yaml
 








 

