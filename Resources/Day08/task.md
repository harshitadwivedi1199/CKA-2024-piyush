## Task 8/40


In this exercise, you will create a Deployment with multiple replicas. After inspecting the Deployment, you will update its Pod template. You will also be able to use the rollout history to roll back to a previous revision.

> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)


## Replicaset
- Create a new Replicaset based on the nginx image with 3 replicas
- Update the replicas to 4 from the YAML
- Update the replicas to 6 from the command line

```YAML
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: frontend-nginx
  labels:
    app: guestbook
    tier: frontend
spec:
  # modify replicas according to your case
  replicas: 3
  selector:
    matchLabels:
      tier: frontend
  template:
    metadata:
      labels:
        tier: frontend
    spec:
      containers:
      - name: nginx-rs
        image: nginx:latest
```

## Deployment
1. Create a Deployment named `nginx` with 3 replicas. The Pods should use the `nginx:1.23.0` image and the name `nginx`. The Deployment uses the label `tier=backend`. The Pod template should use the label `app=v1`.
2. List the Deployment and ensure the correct number of replicas is running.
3. Update the image to `nginx:1.23.4`.
4. Verify that the change has been rolled out to all replicas.
5. Assign the change cause "Pick up patch version" to the revision.
6. Scale the Deployment to 5 replicas.
7. Have a look at the Deployment rollout history.
8. Revert the Deployment to revision 1.
9. Ensure that the Pods use the image `nginx:1.23.0`.

```
controlplane:~$ kubectl describe deploy nginx                   
Name:                   nginx
Namespace:              default
CreationTimestamp:      Thu, 25 Dec 2025 05:32:24 +0000
Labels:                 tier=backend
Annotations:            deployment.kubernetes.io/revision: 2
                        kubernetes.io/change-cause: Pick up patch version
Selector:               app=v1
Replicas:               3 desired | 3 updated | 3 total | 3 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=v1
  Containers:
   nginx:
    Image:         nginx:1.23.0
    Port:          80/TCP
    Host Port:     0/TCP
    Environment:   <none>
    Mounts:        <none>
  Volumes:         <none>
  Node-Selectors:  <none>
  Tolerations:     <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Available      True    MinimumReplicasAvailable
  Progressing    True    NewReplicaSetAvailable
OldReplicaSets:  nginx-59f6558cd6 (0/0 replicas created)
NewReplicaSet:   nginx-d8c469fcd (3/3 replicas created)
Events:
  Type    Reason             Age    From                   Message
  ----    ------             ----   ----                   -------
  Normal  ScalingReplicaSet  2m28s  deployment-controller  Scaled up replica set nginx-59f6558cd6 from 0 to 3
  Normal  ScalingReplicaSet  94s    deployment-controller  Scaled up replica set nginx-d8c469fcd from 0 to 1
  Normal  ScalingReplicaSet  92s    deployment-controller  Scaled down replica set nginx-59f6558cd6 from 3 to 2
  Normal  ScalingReplicaSet  92s    deployment-controller  Scaled up replica set nginx-d8c469fcd from 1 to 2
  Normal  ScalingReplicaSet  90s    deployment-controller  Scaled down replica set nginx-59f6558cd6 from 2 to 1
  Normal  ScalingReplicaSet  90s    deployment-controller  Scaled up replica set nginx-d8c469fcd from 2 to 3
  Normal  ScalingReplicaSet  88s    deployment-controller  Scaled down replica set nginx-59f6558cd6 from 1 to 0
controlplane:~$

```

```YAML
apiVersion: apps/v1
kind:  Deployment
metadata:
  name: nginx
  labels:
    tier: backend
spec:
  template:
    metadata:
      labels:
        app: v1
      name: nginx
    spec:
      containers:
      - image: nginx:1.23.0
        name: nginx
        ports:
        - containerPort: 80
  replicas: 3
  selector:
    matchLabels:
      app: v1
```

## Troubleshooting the issue
1. Apply the below YAML and fix the issue with it

``` YAML
apiVersion: v1
kind:  Deployment
metadata:
  name: nginx-deploy
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
  replicas: 3
  selector:
    matchLabels:
      env: demo
```
Answer -

``` YAML
apiVersion: apps/v1
kind:  Deployment
metadata:
  name: nginx-deploy
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
  replicas: 3
  selector:
    matchLabels:
      env: demo
```

2. Apply the below YAML and fix the issue with it

``` YAML
apiVersion: v1
kind:  Deployment
metadata:
  name: nginx-deploy
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
  replicas: 3
  selector:
    matchLabels:
      env: dev
```
Answer:
```YAML
apiVersion: apps/v1
kind:  Deployment
metadata:
  name: nginx-deploy
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
  replicas: 3
  selector:
    matchLabels:
      env: demo
```
3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will provide visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.

