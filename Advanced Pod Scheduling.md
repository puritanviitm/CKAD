Lab 13: Advanced Pod Scheduling

---------------------------------------------------------------------
Task 1: Node Affinity
---------------------------------------------------------------------
vi node-aff-pref.yaml

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

kubectl apply -f node-aff-pref.yaml
kubectl get pods -o wide

Remove node label 

kubectl label nodes <node_name> disktype-
deploy same pod again it is getting deployed

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

---------------------------------------------------------------------
Task 2: Pod Affinity
---------------------------------------------------------------------

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
 
