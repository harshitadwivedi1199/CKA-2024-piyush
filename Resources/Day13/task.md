## Task 13/40

In this exercise, you will explore node selectors, labels and selectors along with static pods

> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- Create a pod and try to schedule it manually without the scheduler.
```
controlplane:~$ cat pod1.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: manual-node-nginx
  namespace: default
spec:
  nodeName: controlplane
  containers:
  - name: nginx
    image: nginx
controlplane:~$ kubectl get po -o wide
NAME                               READY   STATUS      RESTARTS   AGE     IP            NODE           NOMINATED NODE   READINESS GATES
manual-node-nginx                  1/1     Running     0          10s     192.168.0.4   controlplane   <none>           <none>
node-debugger-controlplane-27d4s   0/1     Completed   0          8m45s   172.30.1.2    controlplane   <none>           <none>

```
- Login to the control plane node and go to the directory of default static pod manifests and try to restart the control plane components
- Create 3 pods with the name as pod1, pod2 and pod3 based on the nginx image and use labels as env:test, env:dev and env:prod for each of these pods respectively.
- Then using the kubectl commands, filter the pods that have labels dev and prod.
```
controlplane:~$ kubectl get po --selector env=dev
NAME   READY   STATUS    RESTARTS   AGE
pod1   1/1     Running   0          4m32s
controlplane:~$ kubectl get po --selector env=test
NAME   READY   STATUS    RESTARTS   AGE
pod2   1/1     Running   0          52s
controlplane:~$ kubectl get po --selector env=prod
NAME   READY   STATUS    RESTARTS   AGE
pod3   1/1     Running   0          62s
controlplane:~$ kubectl get po -o wide | grep pod
pod1                               1/1     Running     0          5m16s   192.168.1.5   node01         <none>           <none>
pod2                               1/1     Running     0          88s     192.168.1.7   node01         <none>           <none>
pod3                               1/1     Running     0          93s     192.168.1.6   node01         <none>           <none>
controlplane:~$ cat pod-1.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: dev
  name: pod1 
  namespace: default
spec:
  containers:
  - name: nginx-pod1
    image: nginx
    env:
    - name: env
      value: dev

controlplane:~$ cat pod-2.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: test
  name: pod2 
  namespace: default
spec:
  containers:
  - name: nginx-pod2
    image: nginx
    env:
    - name: env
      value: test

controlplane:~$ cat pod-3.yaml 
apiVersion: v1
kind: Pod
metadata:
  labels:
    env: prod
  name: pod3
  namespace: default
spec:
  containers:
  - name: nginx-pod3
    image: nginx
    env:
    - name: env
      value: prod

```

3. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
4. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
