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
