## Maximize pod up-time in Kubernetes by utilizing PriorityClass and PodDisruptionBudget ##

In today's world, Kubernetes is the go-to orchestration engine for scheduling workloads and has been widely adopted by all types of organisations, let it be large or small.

In this article, we will go over two important Kubernetes features, "PriorityClass" and "PodDisruptionBudget", and how they can help in making sure that our mission-critical application is highly available and that it is not evicted in case of resource crunch in the cluster.

Assume you have a mission-critical application running on a Kubernetes cluster. This application is made up of multiple pods that are distributed across several cluster nodes. You want to ensure that the application is highly available and that it can continue to function even if one or more nodes fail due to maintenance or other reasons.

You can accomplish this by specifying in a PDB that a certain number of pods should always be available, even during planned disruptions. Set the minimum number of available pods to two, for example, so that if a node fails, the PDB ensures that at least two pods continue to run on other nodes.

Furthermore, you can use a PriorityClass to prioritise this application's criticality over other applications running on the same cluster. This ensures that when resources are scarce, the Kubernetes scheduler prioritises scheduling the pods of this critical application over less critical workloads.


### What is disruption? ###

Before we dive more into this article, lets first understand what exactly is disruption?

In the context of Kubernetes, a disruption refers to a planned or unplanned event that causes a pod to become unavailable for some amount of time. A disruption can occur for many reasons, it can be voluntary or involuntary as well such as:
- Node maintenance: Any pods that are currently running on a node may become momentarily inaccessible when that node must be brought down for maintenance, such as to apply security updates or hardware upgrades.
- Application updates: When a pod needs to be updated with a new version of the application, the old pod is replaced with a new one. During this process, the pod is unavailable.
- Resource limitations: The Kubernetes scheduler may evict certain running pods to make room for other workloads when the cluster does not have enough resources to sustain all of the running pods.

There can be several other reasons as well, In general, a disruption can cause downtime for the affected pods and the applications they're running, so it's important to plan for disruptions and take steps to minimize their impact on the workloads. For more information please refer this URL https://kubernetes.io/docs/concepts/workloads/pods/disruptions/. 


### What are PriorityClass and PodDisruptionBudget? ###

PodDisruptionBudget (PDB) is a Kubernetes object that specifies the minimum requirements for a set of pods' availability during planned disruptions such as maintenance, upgrades, scaling, or other events that may cause a disruption.

PDBs can be used in a Kubernetes cluster to ensure the high availability of critical workloads. Even during planned disruptions or events, the PDB ensures that a certain number or percentage of pods in a deployment or replica set are always available and running.

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

