## Jobs and Cronjobs in Kubernetes

### Task 1: Jobs in Kubernetes 
```
vi job-pod.yaml
```
```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: jobs-hello
spec:
  template:
    metadata:
      name: jobs-pod
    spec:
      containers:
      - name: jobs-ctr
        image: busybox
        args:
        - /bin/sh
        - -c
        - "echo HELLO WORLD !!!!!"
      restartPolicy: Never
```
```
kubectl apply -f job-pod.yaml
```
```
kubectl get jobs
```
```
kubectl get pods
```

Read logs 
```
kubectl logs <name of jobpod>
```

Describe the job
```
kubectl describe jobs jobs-hello
```
```
kubectl describe pod <pod-name>
```
Delete the job
```
kubectl delete -f job-pod.yaml
```

### Task 2: Cronjobs 

Create a yaml called cron.yaml. Use the content given below to fill the file
```
vi cron.yaml
```
```yaml
 
apiVersion: batch/v1
kind: CronJob
metadata:
  name: cronjob-hello
spec:
  schedule: "*/1 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: cronjob-ctr
            image: busybox
            args:
            - /bin/sh
            - -c
            - "echo Hello World!"
          restartPolicy: OnFailure
```

Open a new tab and run the below commands
```
kubectl get po
```
```
kubectl get job
```
```
kubectl get cronjobs 
```
On the Old Tab run the below commnds
```
kubectl apply -f cronjob.yaml
```
```
kubectl get cronjobs.batch
```
```
kubectl get pod
```
```
kubectl logs cronjob-hello-27991419-lz69c
```
