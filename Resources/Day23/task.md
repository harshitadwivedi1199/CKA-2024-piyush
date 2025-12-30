## Task 23/40

In this exercise, you will learn RBAC and creating role and role binding objects.

### Task details

#### Checking Default User Permissions
Note: I am referring the username as `krishna` here in this task but feel free to use your own name or `adam` as per the previous task.
1. Change to the context to `krishna` that you created in the previous day 22 task.
2. Create a new Pod. What would you expect to happen?

#### Granting Access to the User

- Switch back to the original context with admin permissions. 
- Create a new Role named `pod-reader`. The Role should grant permissions to get, watch and list Pods.
- Create a new RoleBinding named `read-pods`. Map the user `krishna` to the Role named `pod-reader`.
- Make sure that both objects have been created properly.
- Switch to the context named `krishna`.
- Create a new Pod named `nginx` with the image `nginx`. What would you expect to happen?
- List the Pods in the namespace. What would you expect to happen?
- Create a new deploymened named `nginx-deploy`. What would you expect to happen?

```
controlplane:~$ kubectl auth whoami
ATTRIBUTE                                           VALUE
Username                                            kubernetes-admin
Groups                                              [kubeadm:cluster-admins system:authenticated]
Extra: authentication.kubernetes.io/credential-id   [X509SHA256=0921345697e413ef20f038443db9b06d4934ba8e7b702bc3b9b0749566e4c0a5]

controlplane:~$ k auth can-i create po --as adam
no

controlplane:~$ ls
adam.csr  adam.key  csr-manifest.yaml  filesystem  learner.crt  learner.csr  learner.key

controlplane:~$ cat role.yaml 
apiVersion: rbac.authorization.k8s.io/v1
kind: Role
metadata:
  namespace: default
  name: pod-reader
rules:
- apiGroups: [""] # "" indicates the core API group
  resources: ["pods"]
  verbs: ["get", "watch", "list"]
controlplane:~$ cat rbac.yaml 
apiVersion: rbac.authorization.k8s.io/v1
# This role binding allows "jane" to read pods in the "default" namespace.
# You need to already have a Role named "pod-reader" in that namespace.
kind: RoleBinding
metadata:
  name: read-pods
  namespace: default
subjects:
# You can specify more than one "subject"
- kind: User
  name: adam # "name" is case sensitive
  apiGroup: rbac.authorization.k8s.io
roleRef:
  # "roleRef" specifies the binding to a Role / ClusterRole
  kind: Role #this must be Role or ClusterRole
  name: pod-reader # this must match the name of the Role or ClusterRole you wish to bind to
  apiGroup: rbac.authorization.k8s.io

controlplane:~$ kubectl apply -f role.yaml 
role.rbac.authorization.k8s.io/pod-reader created
controlplane:~$ kubectl apply -f rbac.yaml 
rolebinding.rbac.authorization.k8s.io/read-pods created

controlplane:~$ k auth can-i create po --as adam
no
controlplane:~$ kubectl auth can-i list pods --as adam
yes

controlplane:~$ kubectl get --raw /api/v1/namespaces/default/pods \
  --server https://172.30.1.2:6443 \
  --client-certificate=learner.crt \
  --client-key=learner.key \
  --insecure-skip-tls-verify \
  --kubeconfig /dev/null
{"kind":"PodList","apiVersion":"v1","metadata":{"resourceVersion":"6602"},"items":[]}

controlplane:~$ kubectl config set-credentials adam \
  --client-certificate=learner.crt \
  --client-key=learner.key
User "adam" set.

controlplane:~$ kubectl config set-context adam-context \
  --cluster=kubernetes \
  --user=adam \
  --namespace=default
Context "adam-context" created.

controlplane:~$ kubectl config use-context adam-context
Switched to context "adam-context".

controlplane:~$ kubectl config current-context
adam-context

controlplane:~$ kubectl get pods -n kube-system
Error from server (Forbidden): pods is forbidden: User "adam" cannot list resource "pods" in API group "" in the namespace "kube-system"
controlplane:~$

```
  

2. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
3. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
