## RBAC

RBAC stands for Role-Based Access Control. 

It is a method of regulating access to resources based on the roles of individual users within an organization. With RBAC, you can specify what actions (such as create, read, update, delete) a user or group of users can perform on specific Kubernetes resources.

### Task 1: Role and Role Binding

Roles: A role is a set of permissions that define what actions can be performed on Kubernetes resources within a namespace. For example, you can define a role that allows read-only access to pods or a role that allows full control over deployments.

RoleBindings: A role binding binds a role to a user, group, or service account within a namespace. It specifies who has access to the resources defined by the role. For example, you can create a role binding that associates a specific role with a particular user, granting them the permissions defined by that role.

#### Create a new ServiceAccount
```
kubectl get ns
```
```
kubectl create ns ns1

```
```
kubectl create sa -n ns1 sa1 
```
```
kubectl get ns
```

#### Create a new Role and RoleBinding 
```
kubectl create role role1 --verb=list --resource=pods -n ns1
```
```
kubectl create rolebinding role-binding1 --role=role1 --serviceaccount=ns1:sa1 -n ns1
```
Describe the Rold and role binding
```
kubectl -n ns1 describe role,rolebindings.rbac.authorization.k8s.io 
```

#### Test whether you are able to do a GET request to Kubernetes API 
```
kubectl run pod1 --image=nginx -n ns1
```
```
kubectl exec -it -n ns1 pod1 -- /bin/bash 
```
Make an API call to list all the pods in the Namespace ns1
```
curl -v --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default/api/v1/namespaces/ns1/pods 
```
The API call is forbidden
```
exit
```
```
vi pod-sa.yaml
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod2
  namespace: ns1
spec:
  serviceAccountName: sa1
  containers:
  - name: my-container
    image: nginx
```
```
kubectl apply -f pod-sa.yaml
```
```
kubectl exec -it -n ns1 pod2 -- /bin/bash 
```
```
curl -v --cacert /var/run/secrets/kubernetes.io/serviceaccount/ca.crt -H "Authorization: Bearer $(cat /var/run/secrets/kubernetes.io/serviceaccount/token)" https://kubernetes.default/api/v1/namespaces/ns1/pods 
```
You should be able to see the details of all the pods

