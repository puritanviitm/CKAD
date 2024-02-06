Lab 12: Node Labelling and Constraining pods in Kubernetes

---------------------------------------------------------------------
Task 1: Deploy pod in specific node based on node-name / hostname
---------------------------------------------------------------------

vi node-name.yaml

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

kubectl create -f node-name.yaml
kubectl get pods -o wide 
see your pod in defined node
 
---------------------------------------------------------------------
Task 1.1: Node labeling and constraining pods
---------------------------------------------------------------------
list node labels
kubectl get nodes --show-labels

kubectl label nodes <node_name> disktype=ssd

list you nodes to check labels 
kubectl get nodes --show-labels | grep "disktype=ssd"

vi nlns-pod.yaml

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


kubectl apply -f nlns-pod.yaml
kubectl get pods -o wide

---------------------------------------------------------------------
Task 2: Cleanup
---------------------------------------------------------------------
#Delete the objects created in this lab.

kubectl delete -f nlns-pod.yaml 
kubectl label nodes <node_name> disktype-
 
#Verify that the label is deleted.

kubectl get nodes --show-labels | grep ssd 
