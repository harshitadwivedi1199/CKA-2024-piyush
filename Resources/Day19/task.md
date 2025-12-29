## Task 18/40

In this exercise, you will explore config maps, secrets and environment variables
> [!NOTE]
> If you do not already have a Kubernetes cluster, you can create a local Kubernetes cluster by following [Day06 Video](https://youtu.be/RORhczcOrWs)
Also, could you do the port binding at the cluster level if you are using KIND? The Day9 video has the details on how to do that.

### Task details
1. Perform the task given here : https://kubernetes.io/docs/tasks/inject-data-application/distribute-credentials-secure/#define-container-environment-variables-using-secret-data

```
controlplane:~$ kubectl create secret generic backend-user --from-literal=backend-username='backend-admin'
secret/backend-user created
controlplane:~$ kubectl get secret 
NAME           TYPE     DATA   AGE
backend-user   Opaque   1      7s
controlplane:~$ kubectl get secret -o yaml v
Error from server (NotFound): secrets "v" not found
controlplane:~$ kubectl get secret -o yaml backend-user  
apiVersion: v1
data:
  backend-username: YmFja2VuZC1hZG1pbg==
kind: Secret
metadata:
  creationTimestamp: "2025-12-29T04:28:29Z"
  name: backend-user
  namespace: default
  resourceVersion: "3104"
  uid: 7672437e-c579-4705-8607-3ea46978de56
type: Opaque
controlplane:~$ kubectl create -f https://k8s.io/examples/pods/inject/pod-single-secret-env-variable.yaml
pod/env-single-secret created
controlplane:~$ kubectl get po 
NAME                READY   STATUS              RESTARTS   AGE
env-single-secret   0/1     ContainerCreating   0          5s
controlplane:~$ kubectl get po -o yaml v
Error from server (NotFound): pods "v" not found
controlplane:~$ kubectl get po -o yaml env-single-secret 
apiVersion: v1
kind: Pod
metadata:
  annotations:
    cni.projectcalico.org/containerID: 5477bc6bf6bddb368045c675184cc9554c277321f2b1823cd284708ebb2cce27
    cni.projectcalico.org/podIP: 192.168.1.4/32
    cni.projectcalico.org/podIPs: 192.168.1.4/32
  creationTimestamp: "2025-12-29T04:29:07Z"
  generation: 1
  name: env-single-secret
  namespace: default
  resourceVersion: "3176"
  uid: 59dc687f-dabf-4db3-ac5d-cc7e4be8d261
spec:
  containers:
  - env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          key: backend-username
          name: backend-user
    image: nginx
    imagePullPolicy: Always
    name: envars-test-container
    resources: {}
    terminationMessagePath: /dev/termination-log
    terminationMessagePolicy: File
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-ptb29
      readOnly: true
  dnsPolicy: ClusterFirst
  enableServiceLinks: true
  nodeName: node01
  preemptionPolicy: PreemptLowerPriority
  priority: 0
  restartPolicy: Always
  schedulerName: default-scheduler
  securityContext: {}
  serviceAccount: default
  serviceAccountName: default
  terminationGracePeriodSeconds: 30
  tolerations:
  - effect: NoExecute
    key: node.kubernetes.io/not-ready
    operator: Exists
    tolerationSeconds: 300
  - effect: NoExecute
    key: node.kubernetes.io/unreachable
    operator: Exists
    tolerationSeconds: 300
  volumes:
  - name: kube-api-access-ptb29
    projected:
      defaultMode: 420
      sources:
      - serviceAccountToken:
          expirationSeconds: 3607
          path: token
      - configMap:
          items:
          - key: ca.crt
            path: ca.crt
          name: kube-root-ca.crt
      - downwardAPI:
          items:
          - fieldRef:
              apiVersion: v1
              fieldPath: metadata.namespace
            path: namespace
status:
  conditions:
  - lastProbeTime: null
    lastTransitionTime: "2025-12-29T04:29:14Z"
    observedGeneration: 1
    status: "True"
    type: PodReadyToStartContainers
  - lastProbeTime: null
    lastTransitionTime: "2025-12-29T04:29:07Z"
    observedGeneration: 1
    status: "True"
    type: Initialized
  - lastProbeTime: null
    lastTransitionTime: "2025-12-29T04:29:14Z"
    observedGeneration: 1
    status: "True"
    type: Ready
  - lastProbeTime: null
    lastTransitionTime: "2025-12-29T04:29:14Z"
    observedGeneration: 1
    status: "True"
    type: ContainersReady
  - lastProbeTime: null
    lastTransitionTime: "2025-12-29T04:29:07Z"
    observedGeneration: 1
    status: "True"
    type: PodScheduled
  containerStatuses:
  - containerID: containerd://2831c889fef1bef1fc77cf6467d03d81fb79ae68cb9696e4f722caac18ef3ac5
    image: docker.io/library/nginx:latest
    imageID: docker.io/library/nginx@sha256:fb01117203ff38c2f9af91db1a7409459182a37c87cced5cb442d1d8fcc66d19
    lastState: {}
    name: envars-test-container
    ready: true
    resources: {}
    restartCount: 0
    started: true
    state:
      running:
        startedAt: "2025-12-29T04:29:14Z"
    volumeMounts:
    - mountPath: /var/run/secrets/kubernetes.io/serviceaccount
      name: kube-api-access-ptb29
      readOnly: true
      recursiveReadOnly: Disabled
  hostIP: 172.30.2.2
  hostIPs:
  - ip: 172.30.2.2
  observedGeneration: 1
  phase: Running
  podIP: 192.168.1.4
  podIPs:
  - ip: 192.168.1.4
  qosClass: BestEffort
  startTime: "2025-12-29T04:29:07Z"
controlplane:~$ kubectl exec -i -t env-single-secret -- /bin/sh -c 'echo $SECRET_USERNAME'
backend-admin
controlplane:~$ kubectl exec -i -t env-single-secret -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-single-secret
NGINX_VERSION=1.29.4
NJS_VERSION=0.9.4
NJS_RELEASE=1~trixie
PKG_RELEASE=1~trixie
DYNPKG_RELEASE=1~trixie
SECRET_USERNAME=backend-admin
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
TERM=xterm
HOME=/root
controlplane:~$ kubectl create secret generic db-user --from-literal=db-username='db-admin'
secret/db-user created

###### Define container environment variables with data from multiple Secrets

controlplane:~$ cat env-multiple-secret.yaml 
apiVersion: v1
kind: Pod
metadata:
  name: env-multiple-secret
  namespace: default
spec:
  containers:
  - env:
    - name: SECRET_USERNAME
      valueFrom:
        secretKeyRef:
          key: backend-username
          name: backend-user
    - name: DB_USERNAME
      valueFrom:
        secretKeyRef:
          key: db-user
          name: db-username
    image: nginx
    name: envars-test-container
controlplane:~$ vi env-multiple-secret.yaml 
controlplane:~$ kubectl delete -f env-multiple-secret.yaml 
pod "env-multiple-secret" deleted from default namespace
controlplane:~$ kubectl apply -f env-multiple-secret.yaml 
pod/env-multiple-secret created
controlplane:~$ kubectl get po 
NAME                  READY   STATUS    RESTARTS   AGE
env-multiple-secret   1/1     Running   0          4s
controlplane:~$ kubectl exec -it env-multiple-secret -- printenv
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
HOSTNAME=env-multiple-secret
NGINX_VERSION=1.29.4
NJS_VERSION=0.9.4
NJS_RELEASE=1~trixie
PKG_RELEASE=1~trixie
DYNPKG_RELEASE=1~trixie
SECRET_USERNAME=backend-admin
DB_USERNAME=db-admin
KUBERNETES_SERVICE_HOST=10.96.0.1
KUBERNETES_SERVICE_PORT=443
KUBERNETES_SERVICE_PORT_HTTPS=443
KUBERNETES_PORT=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP=tcp://10.96.0.1:443
KUBERNETES_PORT_443_TCP_PROTO=tcp
KUBERNETES_PORT_443_TCP_PORT=443
KUBERNETES_PORT_443_TCP_ADDR=10.96.0.1
TERM=xterm
HOME=/root

###### Example: Provide prod/test credentials to Pods using Secrets

controlplane:~$ cat pod.yaml 
apiVersion: v1
kind: List
items:
- kind: Pod
  apiVersion: v1
  metadata:
    name: prod-db-client-pod
    labels:
      name: prod-db-client
  spec:
    volumes:
    - name: secret-volume
      secret:
        secretName: prod-db-secret
    containers:
    - name: db-client-container
      image: nginx
      volumeMounts:
      - name: secret-volume
        readOnly: true
        mountPath: "/etc/secret-volume"
- kind: Pod
  apiVersion: v1
  metadata:
    name: test-db-client-pod
    labels:
      name: test-db-client
  spec:
    volumes:
    - name: secret-volume
      secret:
        secretName: test-db-secret
    containers:
    - name: db-client-container
      image: nginx
      volumeMounts:
      - name: secret-volume
        readOnly: true
        mountPath: "/etc/secret-volume"
controlplane:~$ 
controlplane:~$ kubectl get secret
NAME             TYPE     DATA   AGE
backend-user     Opaque   1      26m
db-user          Opaque   1      23m
prod-db-secret   Opaque   2      9m21s
test-db-secret   Opaque   2      9m8s
controlplane:~$ kubectl get po | grep client-pod
prod-db-client-pod    1/1     Running   0          7m
test-db-client-pod    1/1     Running   0          7m
controlplane:~$ kubectl exec -it test-db-client-pod  -- bin/sh                                
# cat /etc/secret-volume/username
testuser# 
# cat /etc/secret-volume/password
iluvtests# 
# exit
controlplane:~$ kubectl exec -it prod-db-client-pod  -- bin/sh 
# cat /etc/secret-volume/username
produser# 
# cat /etc/secret-volume/password
Y4nys7f11# 
# exit

```

2. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
3. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
