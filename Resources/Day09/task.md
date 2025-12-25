## Task 9/40


In this exercise, you will create a Deployment and expose a container port for its Pods. You will demonstrate the differences between the service types ClusterIP and NodePort.

> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
> Also, do the node binding at the cluster level if you are using KIND. The Day9 video has the details on how to do that.

### Task details
1. Create a Service named `myapp` of type `ClusterIP` that exposes port 80 and maps to the target port 80.
2. Create a Deployment named `myapp` that creates 1 replica running the image `nginx:1.23.4-alpine`. Expose the container port 80.
3. Scale the Deployment to 2 replicas.
4. Create a temporary Pod using the image `busybox` and run a `wget` command against the IP of the service.
5. Run a `wget` command against the service outside the cluster.
6. Change the service type so the Pods can be reached outside the cluster.
7. Run a `wget` command against the service outside the cluster.
8. Discuss: Can you expose the Pods as a service without a deployment?
9. Discuss: Under what condition would you use the service types `LoadBalancer,` `node port,` `clusterIP,` and `external`?

sol -

```
1. Create a Service named `myapp` of type `ClusterIP` that exposes port 80 and maps to the target port 80.
controlplane:~$ cat svc.yaml 
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
2. Create a Deployment named `myapp` that creates 1 replica running the image `nginx:1.23.4-alpine`. Expose the container port 80.
3. Scale the Deployment to 2 replicas.

controlplane:~$ kubectl create deploy myapp --replicas=1 --image=nginx:1.23.4-alpine
deployment.apps/myapp created
controlplane:~$ kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
myapp   0/1     1            0           6s
controlplane:~$ kubectl edit deploy myapp
deployment.apps/myapp edited

here added port under spec to be exposed -

    spec:
      containers:
      - image: nginx:1.23.4-alpine
        name: nginx
        ports:
        - containerPort: 80

controlplane:~$ kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
myapp   1/1     1            1           117s
controlplane:~$ kubectl get po    
NAME                     READY   STATUS    RESTARTS   AGE
myapp-5b89d77f84-g7vm2   1/1     Running   0          8s
controlplane:~$ kubectl scale deploy myapp  --replicas=2
deployment.apps/myapp scaled

controlplane:~$ kubectl get deploy myapp -o yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  annotations:
    deployment.kubernetes.io/revision: "2"
  creationTimestamp: "2025-12-25T14:52:47Z"
  generation: 3
  labels:
    app: myapp
  name: myapp
  namespace: default
  resourceVersion: "3596"
  uid: bd62f414-825c-455c-afca-f8612ed2cd74
spec:
  progressDeadlineSeconds: 600
  replicas: 2
  revisionHistoryLimit: 10
  selector:
    matchLabels:
      app: myapp
  strategy:
    rollingUpdate:
      maxSurge: 25%
      maxUnavailable: 25%
    type: RollingUpdate
  template:
    metadata:
      labels:
        app: myapp
    spec:
      containers:
      - image: nginx:1.23.4-alpine
        imagePullPolicy: IfNotPresent
        name: nginx
        ports:
        - containerPort: 80
          protocol: TCP
        resources: {}
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      terminationGracePeriodSeconds: 30
status:
  availableReplicas: 2
  conditions:
  - lastTransitionTime: "2025-12-25T14:52:47Z"
    lastUpdateTime: "2025-12-25T14:54:42Z"
    message: ReplicaSet "myapp-5b89d77f84" has successfully progressed.
    reason: NewReplicaSetAvailable
    status: "True"
    type: Progressing
  - lastTransitionTime: "2025-12-25T14:55:12Z"
    lastUpdateTime: "2025-12-25T14:55:12Z"
    message: Deployment has minimum availability.
    reason: MinimumReplicasAvailable
    status: "True"
    type: Available
  observedGeneration: 3
  readyReplicas: 2
  replicas: 2
  updatedReplicas: 2

4. Create a temporary Pod using the image `busybox` and run a `wget` command against the IP of the service.
5. Run a `wget` command against the service outside the cluster.

observation -
i. working from inside the pod
ii. working from controlplane
iii. not working from my local machine

controlplane:~$ kubectl get svc 
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP   6d21h
myapp        ClusterIP   10.109.246.115   <none>        80/TCP    23m

controlplane:~$ kubectl run temp-po \
  --image=busybox \
  --restart=Never \
  --command -- wget -O- http://10.109.246.115 
pod/temp-po created
controlplane:~$ kubectl get pod temp-po
NAME      READY   STATUS      RESTARTS   AGE
temp-po   0/1     Completed   0          8s
controlplane:~$ kubectl logs temp-po
Connecting to 10.109.246.115 (10.109.246.115:80)
writing to stdout
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
-                    100% |********************************|   615  0:00:00 ETA
written to stdout
controlplane:~$ wget http://10.109.246.115 
--2025-12-25 15:04:26--  http://10.109.246.115/
Connecting to 10.109.246.115:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 615 [text/html]
Saving to: 'index.html'

index.html                           100%[=====================================================================>]     615  --.-KB/s    in 0s      

2025-12-25 15:04:26 (113 MB/s) - 'index.html' saved [615/615]

6. Change the service type so the Pods can be reached outside the cluster.
7. Run a `wget` command against the service outside the cluster.

controlplane:~$ cat svc.yaml 
apiVersion: v1
kind: Service
metadata:
  name: myapp
spec:
  selector:
    app: myapp
  type: NodePort
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
      nodePort: 30051

controlplane:~$ kubectl apply -f svc.yaml
service/myapp configured
controlplane:~$ kubectl get svc 
NAME         TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1        <none>        443/TCP        6d21h
myapp        NodePort    10.109.246.115   <none>        80:30051/TCP   26m
controlplane:~$ curl http://10.109.246.115:30051 
^C
controlplane:~$ kubectl get po -o wide 
NAME                     READY   STATUS      RESTARTS   AGE   IP            NODE     NOMINATED NODE   READINESS GATES
myapp-5b89d77f84-g7vm2   1/1     Running     0          29m   192.168.1.5   node01   <none>           <none>
myapp-5b89d77f84-txsvc   1/1     Running     0          29m   192.168.1.6   node01   <none>           <none>
temp-po                  0/1     Completed   0          20m   192.168.1.8   node01   <none>           <none>
controlplane:~$ kubectl get node -o wide
NAME           STATUS   ROLES           AGE     VERSION   INTERNAL-IP   EXTERNAL-IP   OS-IMAGE             KERNEL-VERSION     CONTAINER-RUNTIME
controlplane   Ready    control-plane   6d21h   v1.34.3   172.30.1.2    <none>        Ubuntu 24.04.3 LTS   6.8.0-90-generic   containerd://1.7.28
node01         Ready    <none>          6d21h   v1.34.3   172.30.2.2    <none>        Ubuntu 24.04.3 LTS   6.8.0-90-generic   containerd://1.7.28
controlplane:~$ curl http://172.30.2.2:30051 
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
html { color-scheme: light dark; }
body { width: 35em; margin: 0 auto;
font-family: Tahoma, Verdana, Arial, sans-serif; }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>

controlplane:~$ wget http://172.30.1.2:30051
--2025-12-25 15:30:51--  http://172.30.1.2:30051/
Connecting to 172.30.1.2:30051... connected.
HTTP request sent, awaiting response... 200 OK
Length: 615 [text/html]
Saving to: 'index.html.1'

index.html.1                         100%[=====================================================================>]     615  --.-KB/s    in 0s      

2025-12-25 15:30:51 (146 MB/s) - 'index.html.1' saved [615/615]

controlplane:~$ wget http://172.30.2.2:30051
--2025-12-25 15:31:14--  http://172.30.2.2:30051/
Connecting to 172.30.2.2:30051... connected.
HTTP request sent, awaiting response... 200 OK
Length: 615 [text/html]
Saving to: 'index.html.2'

index.html.2                         100%[=====================================================================>]     615  --.-KB/s    in 0s      

2025-12-25 15:31:14 (80.5 MB/s) - 'index.html.2' saved [615/615]




```



3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.

