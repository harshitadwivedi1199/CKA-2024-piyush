## Task 12/40

In this exercise, you will explore daemonset, cronjob and job in kubernetes

> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- Create a Daemonset as per the demo shown in the video.

```
controlplane:~$ kubectl get po -o wide 
NAME             READY   STATUS    RESTARTS   AGE   IP            NODE           NOMINATED NODE   READINESS GATES
nginx-ds-pxbrp   1/1     Running   0          25s   192.168.1.4   node01         <none>           <none>
nginx-ds-xfnjc   1/1     Running   0          25s   192.168.0.4   controlplane   <none>           <none>
controlplane:~$ ls   
ds.yaml  filesystem
controlplane:~$ cat ds.yaml 
apiVersion: apps/v1
kind:  DaemonSet
metadata:
  name: nginx-ds
  labels:
    env: demo
spec:
  template:
    metadata:
      labels:
        env: demo
      name: nginx
    spec:
      containers:
      - image: nginx
        name: nginx
        ports:
        - containerPort: 80
  selector:
    matchLabels:
      env: demo

```

  
- Undertand the cron syntax and create a cronjob object in kubernetes that prints "40daysofkubernetes" after every 5 minutes and use busybox image
```
controlplane:~$ kubectl get cronjob 
NAME    SCHEDULE      TIMEZONE   SUSPEND   ACTIVE   LAST SCHEDULE   AGE
hello   */5 * * * *   <none>     False     0        <none>          7s
controlplane:~$ cat cronjob.yaml 
apiVersion: batch/v1
kind: CronJob
metadata:
  name: hello
spec:
  schedule: "*/5 * * * *"
  jobTemplate:
    spec:
      template:
        spec:
          containers:
          - name: hello
            image: busybox:1.28
            imagePullPolicy: IfNotPresent
            command:
            - /bin/sh
            - -c
            - date; echo 40daysofkubernetes
          restartPolicy: OnFailure

```
3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
