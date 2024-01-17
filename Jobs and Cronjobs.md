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
        - echo HELLO WORLD !!!!!
      restartPolicy: Never
```
```
kubectl create -f job-pod.yaml
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
HELLO WORLD !!!!!

Describe the job
```
kubectl describe jobs jobs-hello
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
            - echo Hello World!
          restartPolicy: OnFailure
```
```
kubectl create -f cronjob.yaml
```
```
kubectl get cronjobs.batch
```
NAME            SCHEDULE      SUSPEND   ACTIVE   LAST SCHEDULE   AGE
cronjob-hello   */1 * * * *   False     0        28s             42s
```
kubectl get pod
```
NAME                           READY   STATUS      RESTARTS   AGE
cronjob-hello-27991419-lz69c   0/1     Completed   0          42s
```
kubectl logs cronjob-hello-27991419-lz69c
```
Hello World!
