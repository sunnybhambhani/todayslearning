## Beyond Basics: Unlocking hidden capabilities of Kubernetes ##

Kubernetes has been widely adopted, and many organizations use it as their de-facto orchestration engine for running workloads that need to be created and deleted frequently.

In this article, we will go over some of the less well-known or easily overlooked (but critical) features of Kuberentes.

- Pod Disruption Budget
- Taints and Tolerations
- Init Containers
- Pod Security Admission
- Pod priority and preemption (More information around this can be found in my previous article: https://kubernetes.io/blog/2023/01/12/protect-mission-critical-pods-priorityclass/)

### Pod Disruption Budget

Before we dive more into this topic, lets first understand what exactly disruption is?

#### Disruption?

In the context of Kubernetes, a disruption refers to a planned or unplanned event that causes a pod to become unavailable for some amount of time. A disruption can happen for many reasons, it can be voluntary or involuntary such as:
- Node maintenance: Any pods that are currently running on a node may become momentarily inaccessible when that node must be brought down for maintenance, such as to apply security updates or hardware upgrades.
- Application updates: When a pod needs to be updated with a new version of the application, the old pod is replaced with a new one. During this process, the pod is unavailable.
- Resource limitations: The Kubernetes scheduler may evict certain running pods to make room for other workloads.
- There can be several other reasons as well, In general, a disruption can cause downtime for the affected pods and the applications they're running, so it's important to plan for disruptions and take steps to minimize their impact on the workloads. For more information please refer this URL https://kubernetes.io/docs/concepts/workloads/pods/disruptions/. 

**PodDisruptionBudget (PDB)** is a Kubernetes object that specifies the minimum requirements for a set of pods' availability during disruptions such as maintenance, upgrades, scaling, or other events that may cause a disruption.

PodDisruptionBudget is basically used in a Kubernetes cluster to ensure the high availability of critical workloads. Even during planned disruptions or events, the PodDisruptionBudget ensures that a certain number or percentage of pods in a deployment or replica set are always available and running.

PDB prevents the removal of additional pods from service until the minimum availability requirements are met. It adds another layer of reliability and resilience to Kubernetes workloads, ensuring that they are highly available and can continue to operate even when there are disruptions.

Below is a sample manifest file, let's break down the different parts of this file:

```yaml
apiVersion: policy/v1
kind: PodDisruptionBudget
metadata:
  name: test-pdb
spec:
  minAvailable: 2
  selector:
    matchLabels:
      app: test-app
```

- `apiVersion` and `kind`, These two attributes, indicate the Kubernetes API version and the type of object being generated, a PodDisruptionBudget.
- `metadata`, This part includes the object's metadata, such as its name.
- `spec`, The specifics of the PodDisruptionBudget are described in this section. The minAvailable key is set to 2 in this example, indicating that there must always be two pods available. Also, a selection is being used to match the labels of the pods covered by this PDB. Here, we are matching the pods in this instance to the label app: test-app.

In this example PDB makes sure that there are always two pods with the label "app: test-app" available.

Hands-on example:

```bash
$ k get nodes
NAME           STATUS   ROLES           AGE   VERSION
minikube       Ready    control-plane   98s   v1.26.3
minikube-m02   Ready    <none>          55s   v1.26.3
minikube-m03   Ready    <none>          17s   v1.26.3
```

```bash
$ k get pods -o wide
NAME                      READY   STATUS    RESTARTS   AGE   IP           NODE           NOMINATED NODE   READINESS GATES
webapp-645ff4bbd4-cq9rv   1/1     Running   0          37s   10.244.1.4   minikube-m02   <none>           <none>
webapp-645ff4bbd4-gmm9c   1/1     Running   0          36s   10.244.0.4   minikube       <none>           <none>
webapp-645ff4bbd4-wlvkb   1/1     Running   0          39s   10.244.2.4   minikube-m03   <none>           <none>
```

```bash
$ k get pods --show-labels
NAME                      READY   STATUS    RESTARTS   AGE    LABELS
webapp-645ff4bbd4-cq9rv   1/1     Running   0          119s   app=webapp,pod-template-hash=645ff4bbd4
webapp-645ff4bbd4-gmm9c   1/1     Running   0          118s   app=webapp,pod-template-hash=645ff4bbd4
webapp-645ff4bbd4-wlvkb   1/1     Running   0          2m1s   app=webapp,pod-template-hash=645ff4bbd4
```

```bash
$ k get pdb -w
NAME         MIN AVAILABLE   MAX UNAVAILABLE   ALLOWED DISRUPTIONS   AGE
webapp-pdb   2               N/A               1                     72s
```

```bash
webapp-pdb   2               N/A               0                     4m14s
```

```bash
$ k drain minikube-m03 --ignore-daemonsets --force
node/minikube-m03 cordoned
Warning: ignoring DaemonSet-managed Pods: kube-system/kindnet-52xqd, kube-system/kube-proxy-sl2nk
evicting pod kube-system/coredns-787d4945fb-6xszs
evicting pod default/webapp-645ff4bbd4-wfkx2
evicting pod default/webapp-645ff4bbd4-wlvkb
evicting pod default/webapp-645ff4bbd4-m5m78
error when evicting pods/"webapp-645ff4bbd4-m5m78" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
error when evicting pods/"webapp-645ff4bbd4-wfkx2" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
pod/webapp-645ff4bbd4-wlvkb evicted
evicting pod default/webapp-645ff4bbd4-m5m78
error when evicting pods/"webapp-645ff4bbd4-m5m78" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/webapp-645ff4bbd4-wfkx2
error when evicting pods/"webapp-645ff4bbd4-wfkx2" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
pod/coredns-787d4945fb-6xszs evicted
evicting pod default/webapp-645ff4bbd4-m5m78
error when evicting pods/"webapp-645ff4bbd4-m5m78" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/webapp-645ff4bbd4-wfkx2
error when evicting pods/"webapp-645ff4bbd4-wfkx2" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/webapp-645ff4bbd4-m5m78
error when evicting pods/"webapp-645ff4bbd4-m5m78" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/webapp-645ff4bbd4-wfkx2
error when evicting pods/"webapp-645ff4bbd4-wfkx2" -n "default" (will retry after 5s): Cannot evict pod as it would violate the pod's disruption budget.
evicting pod default/webapp-645ff4bbd4-m5m78
```

```bash
$ k get pods -o wide
NAME                      READY   STATUS    RESTARTS   AGE     IP           NODE           NOMINATED NODE   READINESS GATES
webapp-645ff4bbd4-7c8r2   0/1     Pending   0          103s    <none>       <none>         <none>           <none>
webapp-645ff4bbd4-m5m78   1/1     Running   0          2m1s    10.244.2.7   minikube-m03   <none>           <none>
webapp-645ff4bbd4-wfkx2   1/1     Running   0          2m35s   10.244.2.5   minikube-m03   <none>           <none>
```
