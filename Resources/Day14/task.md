## Task 14/40

In this exercise, you will explore Taints and Tolerations in Kubernetes
> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- Taint both of your worker nodes as below

   worker01--> gpu=true:NoSchedule , worker02--> gpu=false:NoSchedule
- Create a new pod with the image nginx and see why it's not getting scheduled on worker nodes and control plane nodes.
- Create a toleration on the pod gpu=true:NoSchedule to match with the taint on worker01
- The pod should be scheduled now on worker01
- Delete the taint on the control plane node
- Create a new pod with the image redis , it should be scheduled on control plane node
- Add the taint back on the control plane node(the one that was removed)

```
controlplane:~$ kubectl describe node controlplane | grep Taints
Taints:             gpu=false:NoSchedule
controlplane:~$ kubectl describe node node01 | grep Taints
Taints:             gpu=true:NoSchedule

controlplane:~$ kubectl get po -o wide
NAME                               READY   STATUS      RESTARTS   AGE   IP            NODE           NOMINATED NODE   READINESS GATES
nginx-pod                          1/1     Running     0          4s    192.168.0.6   controlplane   <none>           <none>
node-debugger-controlplane-27d4s   0/1     Completed   0          43m   172.30.1.2    controlplane   <none>           <none>
controlplane:~$ kubectl get po -o wide
NAME                               READY   STATUS      RESTARTS   AGE    IP            NODE           NOMINATED NODE   READINESS GATES
nginx-pod                          1/1     Running     0          4s     192.168.0.8   controlplane   <none>           <none>
node-debugger-controlplane-27d4s   0/1     Completed   0          51m    172.30.1.2    controlplane   <none>           <none>
redis-pod                          1/1     Running     0          2m3s   192.168.1.8   node01         <none>           <none>

controlplane:~$ cat pod-nginx.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: nginx-pod
  namespace: default
spec:
  containers:
  - name: nginx
    image: nginx
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "false"
    effect: "NoSchedule"
controlplane:~$ cat pod1.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: redis-pod
  namespace: default
spec:
  containers:
  - name: redis
    image: redis
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
controlplane:~$ 
```

3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
