## Task 16/40

In this exercise, you will explore Resource requests and limits in Kubernetes
> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
- login to your cluster and create a new namespace with the name mem-example
- Install metrics server using the yaml provided in this repo
- Perform the steps given in the below doc:
  
https://kubernetes.io/docs/tasks/configure-pod-container/assign-memory-resource/#specify-a-memory-request-and-a-memory-limit

```
controlplane:~$ kubectl create ns mem-example
namespace/mem-example created
controlplane:~$ vi memory.yaml
controlplane:~$ kubectl apply -f memory.yaml
serviceaccount/metrics-server created
clusterrole.rbac.authorization.k8s.io/system:aggregated-metrics-reader created
clusterrole.rbac.authorization.k8s.io/system:metrics-server created
rolebinding.rbac.authorization.k8s.io/metrics-server-auth-reader created
clusterrolebinding.rbac.authorization.k8s.io/metrics-server:system:auth-delegator created
clusterrolebinding.rbac.authorization.k8s.io/system:metrics-server created
service/metrics-server created
deployment.apps/metrics-server created
apiservice.apiregistration.k8s.io/v1beta1.metrics.k8s.io created
controlplane:~$ vi memory-pod1.yaml
controlplane:~$ kubectl apply -f memory-pod1.yaml 
pod/memory-demo created
controlplane:~$ kubectl get po 
NAME      READY   STATUS    RESTARTS   AGE
pod1      1/1     Running   0          46m
redis-3   1/1     Running   0          24m
controlplane:~$ kubectl get po 
NAME      READY   STATUS    RESTARTS   AGE
pod1      1/1     Running   0          47m
redis-3   1/1     Running   0          24m
controlplane:~$ kubectl get po -n mem-example
NAME          READY   STATUS    RESTARTS   AGE
memory-demo   1/1     Running   0          22s
controlplane:~$ kubectl top pod memory-demo --namespace=mem-example
NAME          CPU(cores)   MEMORY(bytes)   
memory-demo   55m          150Mi           
controlplane:~$ vi memory-pod2.yaml 
controlplane:~$ kubectl apply -f memory-pod2.yaml 
pod/memory-demo-2 created
controlplane:~$ kubectl get po -n mem-example
NAME            READY   STATUS             RESTARTS     AGE
memory-demo     1/1     Running            0            90s
memory-demo-2   0/1     CrashLoopBackOff   1 (3s ago)   5s
controlplane:~$ kubectl describe po memory-demo-2 -n mem-example
Name:             memory-demo-2
Namespace:        mem-example
Priority:         0
Service Account:  default
Node:             node01/172.30.2.2
Start Time:       Sat, 27 Dec 2025 15:39:46 +0000
Labels:           <none>
Annotations:      cni.projectcalico.org/containerID: 3eeac8cbbaaf9419093079008374b5ae4f305ed4e403757f691045f4b181ba2a
                  cni.projectcalico.org/podIP: 192.168.1.9/32
                  cni.projectcalico.org/podIPs: 192.168.1.9/32
Status:           Running
IP:               192.168.1.9
IPs:
  IP:  192.168.1.9
Containers:
  memory-demo-2-ctr:
    Container ID:  containerd://577267e5d8ded5c7a09ebbf9d31711ab3e7f71a80b643126b94a0b9ba645b45d
    Image:         polinux/stress
    Image ID:      docker.io/polinux/stress@sha256:b6144f84f9c15dac80deb48d3a646b55c7043ab1d83ea0a697c09097aaad21aa
    Port:          <none>
    Host Port:     <none>
    Command:
      stress
    Args:
      --vm
      1
      --vm-bytes
      250M
      --vm-hang
      1
    State:          Terminated
      Reason:       OOMKilled
      Exit Code:    137
      Started:      Sat, 27 Dec 2025 15:40:03 +0000
      Finished:     Sat, 27 Dec 2025 15:40:03 +0000
    Last State:     Terminated
      Reason:       OOMKilled
      Exit Code:    137
      Started:      Sat, 27 Dec 2025 15:39:48 +0000
      Finished:     Sat, 27 Dec 2025 15:39:48 +0000
    Ready:          False
    Restart Count:  2
    Limits:
      memory:  100Mi
    Requests:
      memory:     50Mi
    Environment:  <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from kube-api-access-ts927 (ro)
Conditions:
  Type                        Status
  PodReadyToStartContainers   True 
  Initialized                 True 
  Ready                       False 
  ContainersReady             False 
  PodScheduled                True 
Volumes:
  kube-api-access-ts927:
    Type:                    Projected (a volume that contains injected data from multiple sources)
    TokenExpirationSeconds:  3607
    ConfigMapName:           kube-root-ca.crt
    Optional:                false
    DownwardAPI:             true
QoS Class:                   Burstable
Node-Selectors:              <none>
Tolerations:                 node.kubernetes.io/not-ready:NoExecute op=Exists for 300s
                             node.kubernetes.io/unreachable:NoExecute op=Exists for 300s
Events:
  Type     Reason     Age               From               Message
  ----     ------     ----              ----               -------
  Normal   Scheduled  19s               default-scheduler  Successfully assigned mem-example/memory-demo-2 to node01
  Normal   Pulled     19s               kubelet            Successfully pulled image "polinux/stress" in 234ms (234ms including waiting). Image size: 4041495 bytes.
  Normal   Pulled     18s               kubelet            Successfully pulled image "polinux/stress" in 238ms (238ms including waiting). Image size: 4041495 bytes.
  Normal   Pulling    4s (x3 over 19s)  kubelet            Pulling image "polinux/stress"
  Normal   Created    3s (x3 over 19s)  kubelet            Created container: memory-demo-2-ctr
  Normal   Started    3s (x3 over 19s)  kubelet            Started container memory-demo-2-ctr
  Warning  BackOff    3s (x3 over 17s)  kubelet            Back-off restarting failed container memory-demo-2-ctr in pod memory-demo-2_mem-example(e1c3c702-2055-4e47-985e-2ee142b177c6)
  Normal   Pulled     3s                kubelet            Successfully pulled image "polinux/stress" in 226ms (226ms including waiting). Image size: 4041495 bytes.
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
