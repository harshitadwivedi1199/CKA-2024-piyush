## Task 15/40

In this exercise, you will explore Node Affinity in Kubernetes
> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- create a pod with nginx as the image and add the nodeffinity with property requiredDuringSchedulingIgnoredDuringExecution and condition disktype = ssd
- check the status of the pod and see why it is not scheduled
- add the label to your worker01 node as disktype=ssd and then check the status of the pod
- It should be scheduled on worker node 1
- create a new pod with redis as the image and add the nodeaffinity with property requiredDuringSchedulingIgnoredDuringExecution and condition disktype without any value
- add the label to worker02 node with disktype and no value
- ensure that pod2 should be scheduled on worker02 node

```
controlplane:~$ kubectl apply -f pod.yaml 
pod/pod1 created
controlplane:~$ kubectl get po
NAME   READY   STATUS    RESTARTS   AGE
pod1   0/1     Pending   0          5s
controlplane:~$ kubectl describe po pod1
Name:             pod1
Namespace:        default
Priority:         0
Service Account:  default
Node:             <none>
Labels:           env=dev
Annotations:      <none>
Status:           Pending
IP:               
IPs:              <none>
Containers:
  nginx-pod1:
    Image:      nginx
    Port:       <none>
    Host Port:  <none>
    Environment:
      env:  dev
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-vlbww (ro)
Conditions:
  Type           Status
  PodScheduled   False 
Volumes:
  kube-api-access-vlbww:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   BestEffort
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason            Age   From               Message
  ----     ------            ----  ----               -------
  Warning  FailedScheduling  16s   default-scheduler  0/2 nodes are available: 2 node(s) didn't match Pod's node affinity/selector. no new claims to deallocate, preemption: 0/2 nodes are available: 2 Preemption is not helpful for scheduling.
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: dev
  name: pod1 
  namespace: default
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
          nodeSelectorTerms:
          - matchExpressions:
              - key: disktype
                operator: In
                values:
                - ssd
  containers:
  - name: nginx-pod1
    image: nginx
    env:
    - name: env
      value: dev

controlplane:~$ kubectl get node
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   8d    v1.34.3
node01         Ready    <none>          8d    v1.34.3
controlplane:~$ kubectl label node node01 disktype=ssd
node/node01 labeled
controlplane:~$ kubectl get po
NAME   READY   STATUS    RESTARTS   AGE
pod1   1/1     Running   0          2m8s
controlplane:~$ kubectl get po -o wide
NAME   READY   STATUS    RESTARTS   AGE     IP            NODE     NOMINATED NODE   READINESS GATES
pod1   1/1     Running   0          2m13s   192.168.1.4   node01   <none>           <none>


```

3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
