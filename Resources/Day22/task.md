## Task 22/40

In this exercise, you will learn Authentication and Authorization in Kubernetes

### Task details
1. Try to run the commands and steps as per the video and document your learning

```
controlplane:~$ kubectl get csr learner-user-csr   -o jsonpath='{.status.certificate}'   | base64 --decode > learner.crt 
controlplane:~$ kubectl get --raw /api/v1/namespaces/default/pods   --server https://localhost:64418   --client-certificate learner.crt   --client-key learner.key   --insecure-skip-tls-verify   --kubeconfig /dev/null
The connection to the server localhost:64418 was refused - did you specify the right host or port?

controlplane:~$ kubectl config view --minify
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: DATA+OMITTED
    server: https://172.30.1.2:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
users:
- name: kubernetes-admin
  user:
    client-certificate-data: DATA+OMITTED
    client-key-data: DATA+OMITTED
controlplane:~$ kubectl get --raw /api/v1/namespaces/default/pods \
  --server https://172.30.1.2:6443 \
  --client-certificate=learner.crt \
  --client-key=learner.key \
  --insecure-skip-tls-verify \
  --kubeconfig /dev/null
Error from server (Forbidden): pods is forbidden: User "adam" cannot list resource "pods" in API group "" in the namespace "default"



```

2. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
3. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
