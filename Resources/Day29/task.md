## Task 29/40

In this exercise, you will explore Kubernetes volumes.

> [!NOTE]
> Make sure you have a Kubernetes cluster created setup on virtual machines, you can follow [this video](https://youtu.be/WcdMC3Lj4tU) as a pre-requisites

### Task details
1. Create a PersistentVolume named `pv-demo`, access mode `ReadWriteMany`, 512Mi of storage capacity and the host path `/data/config`.
2. Create a PersistentVolumeClaim named `pvc-demo`. The claim should request 256Mi and use an empty string value for the storage class. Please make sure that the PersistentVolumeClaim is properly bound after its creation.
3. Mount the PersistentVolumeClaim from a new Pod named `app` with the path `/var/app/config`. The Pod uses the image `nginx:latest`.
4. Open an interactive shell to the Pod and create a file in the directory `/var/app/config`.

```
controlplane:~$ cat pv.yaml 
apiVersion: v1
kind: PersistentVolume
metadata:
  name: pv-demo
  labels:
    type: local
spec:
  capacity:
    storage: 512Mi
  accessModes:
    - ReadWriteMany
  hostPath:
    path: "/data/config"
controlplane:~$ cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pvc-demo
spec:
  storageClassName: ""
  accessModes:
    - ReadWriteMany
  resources:
    requests:
      storage: 256Mi

controlplane:~$ kubectl apply -f pv.yaml 
persistentvolume/pv-demo created
controlplane:~$ kubectl get pv 
NAME      CAPACITY   ACCESS MODES   RECLAIM POLICY   STATUS      CLAIM   STORAGECLASS   VOLUMEATTRIBUTESCLASS   REASON   AGE
pv-demo   512Mi      RWX            Retain           Available                          <unset>                          9s
controlplane:~$ kubectl get pvc
No resources found in default namespace.
controlplane:~$ kubectl apply -f pvc.yaml
persistentvolumeclaim/pvc-demo created
controlplane:~$ kubectl get pvc 
NAME       STATUS   VOLUME    CAPACITY   ACCESS MODES   STORAGECLASS   VOLUMEATTRIBUTESCLASS   AGE
pvc-demo   Bound    pv-demo   512Mi      RWX                           <unset>                 6s
controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  volumes:
    - name: app-storage
      persistentVolumeClaim:
        claimName: pvc-demo
  containers:
    - name: app
      image: nginx:latest
      ports:
        - containerPort: 80
          name: "http-server"
      volumeMounts:
        - mountPath: "/var/app/config"
          name: app-storage
controlplane:~$ kubectl apply -f pod.yaml 
pod/app created
controlplane:~$ kubectl get po 
NAME   READY   STATUS              RESTARTS   AGE
app    0/1     ContainerCreating   0          5s
controlplane:~$ kubectl get po 
NAME   READY   STATUS    RESTARTS   AGE
app    1/1     Running   0          8m3s
controlplane:~$ kubectl exec -it app -- bash 
root@app:/# ls
bin  boot  dev  docker-entrypoint.d  docker-entrypoint.sh  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
root@app:/# cd /var/app/config
root@app:/var/app/config# ls
root@app:/var/app/config# touch hello.txt
root@app:/var/app/config# vi hello.txt 
bash: vi: command not found
root@app:/var/app/config# echo "Hello harshi!!!" > hello.txt 
echo "Hello harshivi hello.txt !" > hello.txt 
root@app:/var/app/config# cat hello.txt 
Hello harshivi hello.txt !
root@app:/var/app/config# 
root@app:/var/app/config# 
root@app:/var/app/config# 
root@app:/var/app/config# exit 
exit
controlplane:~$ kubect get po 
Command 'kubect' not found, did you mean:
  command 'kubectx' from deb kubectx (0.9.5-1ubuntu0.4)
Try: apt install <deb name>
controlplane:~$ kubectl get po 
NAME   READY   STATUS    RESTARTS   AGE
app    1/1     Running   0          10m
controlplane:~$ kubectl delete po app 
pod "app" deleted from default namespace
controlplane:~$ kubectl apply -f pod.yaml 
pod/app created
controlplane:~$ kubectl exec -it app -- bash 
root@app:/# cd /var/app/config
root@app:/var/app/config# ls
hello.txt
root@app:/var/app/config# cat hello.txt 
Hello harshivi hello.txt !
root@app:/var/app/config# 
root@app:/var/app/config# exit 
exit
controlplane:~$ 
```
   
6. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
7. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
