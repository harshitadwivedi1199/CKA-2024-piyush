## Task 24/40

In this exercise, you will learn Authentication and Authorization in Kubernetes

### Task details
1. Try to run the commands and steps as per the video and document your learning

```
controlplane:~$ kubectl api-resources --namespaced=true
NAME                        SHORTNAMES   APIVERSION                     NAMESPACED   KIND
bindings                                 v1                             true         Binding
configmaps                  cm           v1                             true         ConfigMap
endpoints                   ep           v1                             true         Endpoints
events                      ev           v1                             true         Event
limitranges                 limits       v1                             true         LimitRange
persistentvolumeclaims      pvc          v1                             true         PersistentVolumeClaim
pods                        po           v1                             true         Pod
podtemplates                             v1                             true         PodTemplate
replicationcontrollers      rc           v1                             true         ReplicationController
resourcequotas              quota        v1                             true         ResourceQuota
secrets                                  v1                             true         Secret
serviceaccounts             sa           v1                             true         ServiceAccount
services                    svc          v1                             true         Service
controllerrevisions                      apps/v1                        true         ControllerRevision
daemonsets                  ds           apps/v1                        true         DaemonSet
deployments                 deploy       apps/v1                        true         Deployment
replicasets                 rs           apps/v1                        true         ReplicaSet
statefulsets                sts          apps/v1                        true         StatefulSet
localsubjectaccessreviews                authorization.k8s.io/v1        true         LocalSubjectAccessReview
horizontalpodautoscalers    hpa          autoscaling/v2                 true         HorizontalPodAutoscaler
cronjobs                    cj           batch/v1                       true         CronJob
jobs                                     batch/v1                       true         Job
leases                                   coordination.k8s.io/v1         true         Lease
networkpolicies                          crd.projectcalico.org/v1       true         NetworkPolicy
networksets                              crd.projectcalico.org/v1       true         NetworkSet
endpointslices                           discovery.k8s.io/v1            true         EndpointSlice
events                      ev           events.k8s.io/v1               true         Event
ingresses                   ing          networking.k8s.io/v1           true         Ingress
networkpolicies             netpol       networking.k8s.io/v1           true         NetworkPolicy
poddisruptionbudgets        pdb          policy/v1                      true         PodDisruptionBudget
rolebindings                             rbac.authorization.k8s.io/v1   true         RoleBinding
roles                                    rbac.authorization.k8s.io/v1   true         Role
resourceclaims                           resource.k8s.io/v1             true         ResourceClaim
resourceclaimtemplates                   resource.k8s.io/v1             true         ResourceClaimTemplate
csistoragecapacities                     storage.k8s.io/v1              true         CSIStorageCapacity
controlplane:~$ kubectl api-resources --namespaced=false
NAME                                SHORTNAMES   APIVERSION                        NAMESPACED   KIND
componentstatuses                   cs           v1                                false        ComponentStatus
namespaces                          ns           v1                                false        Namespace
nodes                               no           v1                                false        Node
persistentvolumes                   pv           v1                                false        PersistentVolume
mutatingwebhookconfigurations                    admissionregistration.k8s.io/v1   false        MutatingWebhookConfiguration
validatingadmissionpolicies                      admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicy
validatingadmissionpolicybindings                admissionregistration.k8s.io/v1   false        ValidatingAdmissionPolicyBinding
validatingwebhookconfigurations                  admissionregistration.k8s.io/v1   false        ValidatingWebhookConfiguration
customresourcedefinitions           crd,crds     apiextensions.k8s.io/v1           false        CustomResourceDefinition
apiservices                                      apiregistration.k8s.io/v1         false        APIService
selfsubjectreviews                               authentication.k8s.io/v1          false        SelfSubjectReview
tokenreviews                                     authentication.k8s.io/v1          false        TokenReview
selfsubjectaccessreviews                         authorization.k8s.io/v1           false        SelfSubjectAccessReview
selfsubjectrulesreviews                          authorization.k8s.io/v1           false        SelfSubjectRulesReview
subjectaccessreviews                             authorization.k8s.io/v1           false        SubjectAccessReview
certificatesigningrequests          csr          certificates.k8s.io/v1            false        CertificateSigningRequest
bgpconfigurations                                crd.projectcalico.org/v1          false        BGPConfiguration
bgppeers                                         crd.projectcalico.org/v1          false        BGPPeer
blockaffinities                                  crd.projectcalico.org/v1          false        BlockAffinity
caliconodestatuses                               crd.projectcalico.org/v1          false        CalicoNodeStatus
clusterinformations                              crd.projectcalico.org/v1          false        ClusterInformation
felixconfigurations                              crd.projectcalico.org/v1          false        FelixConfiguration
globalnetworkpolicies                            crd.projectcalico.org/v1          false        GlobalNetworkPolicy
globalnetworksets                                crd.projectcalico.org/v1          false        GlobalNetworkSet
hostendpoints                                    crd.projectcalico.org/v1          false        HostEndpoint
ipamblocks                                       crd.projectcalico.org/v1          false        IPAMBlock
ipamconfigs                                      crd.projectcalico.org/v1          false        IPAMConfig
ipamhandles                                      crd.projectcalico.org/v1          false        IPAMHandle
ippools                                          crd.projectcalico.org/v1          false        IPPool
ipreservations                                   crd.projectcalico.org/v1          false        IPReservation
kubecontrollersconfigurations                    crd.projectcalico.org/v1          false        KubeControllersConfiguration
flowschemas                                      flowcontrol.apiserver.k8s.io/v1   false        FlowSchema
prioritylevelconfigurations                      flowcontrol.apiserver.k8s.io/v1   false        PriorityLevelConfiguration
ingressclasses                                   networking.k8s.io/v1              false        IngressClass
ipaddresses                         ip           networking.k8s.io/v1              false        IPAddress
servicecidrs                                     networking.k8s.io/v1              false        ServiceCIDR
runtimeclasses                                   node.k8s.io/v1                    false        RuntimeClass
clusterrolebindings                              rbac.authorization.k8s.io/v1      false        ClusterRoleBinding
clusterroles                                     rbac.authorization.k8s.io/v1      false        ClusterRole
deviceclasses                                    resource.k8s.io/v1                false        DeviceClass
resourceslices                                   resource.k8s.io/v1                false        ResourceSlice
priorityclasses                     pc           scheduling.k8s.io/v1              false        PriorityClass
csidrivers                                       storage.k8s.io/v1                 false        CSIDriver
csinodes                                         storage.k8s.io/v1                 false        CSINode
storageclasses                      sc           storage.k8s.io/v1                 false        StorageClass
volumeattachments                                storage.k8s.io/v1                 false        VolumeAttachment
volumeattributesclasses             vac          storage.k8s.io/v1                 false        VolumeAttributesClass
controlplane:~$

controlplane:~$ kubectl get pods -n kube-system
Error from server (Forbidden): pods is forbidden: User "adam" cannot list resource "pods" in API group "" in the namespace "kube-system"
controlplane:~$ kubectl auth can-i list nodes --as adam
Error from server (Forbidden): users "adam" is forbidden: User "adam" cannot impersonate resource "users" in API group "" at the cluster scope
controlplane:~$ kubectl create clusterrole node-reader \
  --verb=get,list,watch \
  --resource=nodes
Error from server (Forbidden): clusterroles.rbac.authorization.k8s.io is forbidden: User "adam" cannot create resource "clusterroles" in API group "rbac.authorization.k8s.io" at the cluster scope
controlplane:~$ kubectl config get-contexts
CURRENT   NAME                          CLUSTER      AUTHINFO           NAMESPACE
*         adam-context                  kubernetes   adam               default
          kubernetes-admin@kubernetes   kubernetes   kubernetes-admin   
controlplane:~$ kubectl config use-context  kubernetes-admin@kubernetes 
Switched to context "kubernetes-admin@kubernetes".
controlplane:~$ kubectl create clusterrole node-reader   --verb=get,list,watch   --resource=nodes
clusterrole.rbac.authorization.k8s.io/node-reader created
controlplane:~$ kubectl create clusterrolebinding adam-node-reader \
  --clusterrole=node-reader \
  --user=adam
clusterrolebinding.rbac.authorization.k8s.io/adam-node-reader created
controlplane:~$ kubectl auth can-i list nodes --as adam
Warning: resource 'nodes' is not namespace scoped

yes
controlplane:~$ kubectl config use-context adam-context
Switched to context "adam-context".
controlplane:~$ kubectl get node
NAME           STATUS   ROLES           AGE   VERSION
controlplane   Ready    control-plane   11d   v1.34.3
node01         Ready    <none>          11d   v1.34.3

```

2. **Share your learnings**: Document your key takeaways and insights in a blog post and social media update
3. **Make it public**: Share what you learn publicly on LinkedIn or Twitter.
   - **Tag us and use the hashtag**: Include the following in your post:
     - Tag [@PiyushSachdeva](https://www.linkedin.com/in/piyush-sachdeva) and [@CloudOps Community](https://www.linkedin.com/company/thecloudopscomm) (on both platforms)
     - Use the hashtag **#40daysofkubernetes**
     - **Embed the video**: Enhance your blog post by embedding the video lesson from the Kubernetes series. This will give you visual context and reinforce your written explanations.

## Blog Post Focus 📝

- **Clarity is essential**: Write your blog post clearly and concisely, making it easy for anyone to grasp the concepts, regardless of their prior Kubernetes experience.
