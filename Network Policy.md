## Network Policy

### Task 1: Check the network connectivity between pods in different namespaces
Create 2 namespaces
```
kubectl create ns ns1
```
```
kubectl create ns ns2
```
Create pod in first namespace
```
kubectl -n ns1 run ns1-pod --image nginx 
```
Create pod in second namespace
```
kubectl -n ns2 run ns2-pod --image nginx 
```
Enter the pod in first namespace  and check its connectivity with a pod in second namespace
```
kubectl exec -it -n ns1 ns1-pod -- sh
```
Ping with the IP address of second pod
```
apt update && apt install iputils-ping
```
```
ping -c 3 <Ip Address of second pod>
```
```
exit
```
Enter the pod in second namespace  and check its connectivity with a pod in first namespace
```
kubectl -n ns2 exec -it ns2-pod -- sh
```
Ping with the IP address of first pod
```
apt update && apt install iputils-ping
```
```
ping -c 3 <Ip Address of first pod>
```
```
exit
```

### Task 2: Ingress Network policy with Pod labels 

Create a nginx pod and service with labels role=backend
```
kubectl run backend --image nginx -l role=backend
```
```
kubectl expose po backend --port 80 
```
```
kubectl get pod
```
```
kubectl get svc
```
Create a new busybox pod and verify that it can access the backend service.
Then, exit out of the container
```
kubectl run --rm -it --image=busybox net-policy 
```
```
wget -qO- -T3 http://backend   #curl http://backend
```
```
exit
```
Create a network policy which uses labels to deny all ingress traffic
```
vi np-deny-all.yml
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress: []
```
```
kubectl apply -f np-deny-all.yml
```
```
kubectl get networkpolicies
```
Create a new busybox pod again and verify that it cannot access the backend service
```
kubectl run --rm -it --image=busybox net-policy
```
```
wget -qO- -T3 http://backend
```
It will show timeout
```
exit
```
Modify the network policy to allow traffic with matching Pod labels 
Inspect the network policy and notice the selectors
```
vi np-pod-label-allow.yml
```
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: backend-policy
spec:
  podSelector:
    matchLabels:
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          role: frontend		  
```
Before applying this yaml describe networkpolicies to check rule
```
kubectl describe networkpolicies backend-policy
```
```
kubectl apply -f np-pod-label-allow.yml
```
```
kubectl describe networkpolicies backend-policy
```
Run the busybox pod again and verify that it is able to access backend service.
Notice the labels on the Pod. Inspect the network policy and notice the selectors
```
kubectl run --rm -it --image=busybox --labels role=frontend net-policy
```
```
wget -qO- -T3 http://backend
```
```
exit
```
Run the busybox pod again without labels and notice that the backend service is not accessible any more.
```
kubectl run --rm -it --image=busybox net-policy 
```
```
wget -qO- -T3 http://backend
```
```
exit
```

### Task 3: Egrees Policy
```
vi egress-policy.yaml
```
Create a Kubernetes Network Policy manifest file to define egress rules.
```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-outbound
spec:
  podSelector: {}
  policyTypes:
  - Egress
  egress:
  - to:
    - ipBlock:
        cidr: 8.8.8.8/32  # Example IP address
```
Apply the network policy 
```
kubectl apply -f egress-policy.yaml
```
Verify that the egress policy has been applied to your pods:
```
kubectl get networkpolicies
```
Deploy a test pod within the namespace where the network policy is applied.
```
vi  test-pod.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: test-pod
spec:
  containers:
  - name: test-container
    image: busybox
    command: ["sleep", "3600"]  # Keep the pod running for testing
```
```
kubectl apply -f test-pod.yaml
```
Exec into the test pod:
```
kubectl exec -it test-pod -- /bin/sh
```
curl http://8.8.8.8
```
Verify that connections to allowed destinations are successful.

Attempt to access destinations that are not allowed by the egress policy:
```
curl http://yahoo.com
```
Verify that connections to disallowed destinations are blocked or result in errors.
Inspect Network Traffic:

Capture outgoing traffic from the test pod:
bash
Copy code
kubectl exec -it test-pod -- tcpdump -i eth0 -w /tmp/outgoing.pcap
Trigger traffic from within the pod as described above.
Stop the packet capture:
sql
Copy code
kubectl exec -it test-pod -- pkill tcpdump
Transfer the captured file from the pod to your local machine:
bash
Copy code
kubectl cp test-pod:/tmp/outgoing.pcap ./outgoing.pcap
Analyze the captured traffic using Wireshark to ensure that only allowed traffic is present.
Check Logs:

Check logs from your network policy provider for any denied egress traffic.
Check Kubernetes events for any policy violations:
arduino
Copy code
kubectl get events

