## Task 11/40


In this exercise, you will explore environment variables and multi-container pods in Kubernetes.

> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- Create a multi-container pod as per the demo shown in the video.

```

controlplane:~$ kubectl get svc -o wide
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE    SELECTOR
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   8d     <none>
mydb         ClusterIP   10.106.228.110   <none>        80/TCP    10s    app.kubernetes.io/name=MyApp
myservice    ClusterIP   10.111.235.220   <none>        80/TCP    114s   app.kubernetes.io/name=MyApp
controlplane:~$ kubectl get po 
NAME       READY   STATUS    RESTARTS   AGE
test-pod   1/1     Running   0          14m

## pod logs after creating both services

controlplane:~$ kubectl logs test-pod  -f
Defaulted container "main-container" out of: main-container, init-container (init), init-mydb (init)
user is Harshita
controlplane:~$ cat pod-init.yaml        
apiVersion: v1
kind: Pod
metadata:
 name: test-pod
spec:
 containers:
  - name: main-container
    image: busybox:1.28
    env:
    - name: User
      value: Harshita
    command: ['sh' ,'-c' ,'echo user is $User && sleep 7200' ]
 initContainers:
  - name: init-container
    image: busybox:1.28
    command: ['sh', '-c'] #command to run
    args: ['until nslookup myservice.default.svc.cluster.local; do echo waiting for myservice; sleep 2; done']
  - name: init-mydb
    image: busybox:1.28
    command: ['sh', '-c']
    args: ['until nslookup mydb.default.svc.cluster.local; do echo waiting for mydb; sleep 2; done']
 
### service yaml
    
controlplane:~$ cat svc-myservice.yaml 
apiVersion: v1
kind: Service
metadata:
  name: myservice
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9376

controlplane:~$ cat svc-mydb.yaml 
apiVersion: v1
kind: Service
metadata:
  name: mydb
spec:
  selector:
    app.kubernetes.io/name: MyApp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 9375

```


3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.

