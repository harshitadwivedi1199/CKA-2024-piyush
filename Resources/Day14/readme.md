# Day 14/40 - Taints and Tolerations in Kubernetes 📘🚀

## Check out the video below for Day14 👇

[![Day14/40 - Taints and Tolerations in Kubernetes](https://img.youtube.com/vi/nwoS2tK2s6Q/sddefault.jpg)](https://youtu.be/nwoS2tK2s6Q)

# Taints and Tolerations in Kubernetes 🚧📜

In this guide, we'll explore taints and tolerations in Kubernetes, essential tools for managing where pods can be scheduled in your cluster.

---

## Taints: Putting Up Fences 🚫

Think of taints as "only you are allowed" signs on your Kubernetes nodes. A taint marks a node with a specific characteristic, such as `"gpu=true"`. By default, pods cannot be scheduled on tainted nodes unless they have a special permission called toleration. When a toleration on a pod matches with the taint on the node then only that pod will be scheduled on that node.

---

## Tolerations: Permission Slips for Pods ✅

Toleration allows a pod to say, "Hey, I can handle that taint. Schedule me anyway!" You define tolerations in the pod specification to let them bypass the taints.

---

## Taints & Tolerations in Action 🎬

Here’s a breakdown of the commands to manage taints and tolerations:

### Tainting a Node:

```bash
kubectl taint nodes node1 key=gpu:NoSchedule
```

This command taints node1 with the key "gpu" and the effect "NoSchedule." Pods without a toleration for this taint won't be scheduled there.

To remove the taint , you add - at the end of the command , like below.

```bash
kubectl taint nodes node1 key=gpu:NoSchedule-
```

### Adding toleration to the pod:

```yaml
apiVersion: v1
kind: Pod
metadata:
  labels:
    run: redis
  name: redis
spec:
  containers:
  - image: redis
    name: redis
  tolerations:
  - key: "gpu"
    operator: "Equal"
    value: "true"
    effect: "NoSchedule"
```

>Note: This pod specification defines a toleration for the "gpu" taint with the effect "NoSchedule." This allows the pod to be scheduled on tainted nodes.

## What happens depends on the taint effect:

Taint Effect     | Impact on existing Pods

NoSchedule       > Existing Pods stay (no eviction)

PreferNoSchedule > Existing Pods stay, scheduler avoids new Pods

NoExecute	       > Existing Pods without toleration are evicted


## Taint effects explained

### NoSchedule
kubectl taint nodes node1 key=value:NoSchedule

New Pods without toleration → ❌ blocked

Existing Pods → ✅ continue running

### PreferNoSchedule
kubectl taint nodes node1 key=value:PreferNoSchedule

Soft restriction

Scheduler tries to avoid the node but may still place Pods if needed

### NoExecute
kubectl taint nodes node1 key=value:NoExecute

New Pods without toleration → ❌ blocked

Existing Pods without toleration → ❌ evicted

Pods with toleration can stay (optionally with time limit)

### Labels vs Taints/Tolerations

Labels group nodes based on size, type,env, etc. Unlike taints, labels don't directly affect scheduling but are useful for organizing resources.

### Limitations to Remember 🚧

Taints and tolerations are powerful tools, but they have limitations. They cannot handle complex expressions like "AND" or "OR." 
So, what do we use in that case? We use a combination of Taints, tolerance, and Node affinity, which we will discuss in the next video.

