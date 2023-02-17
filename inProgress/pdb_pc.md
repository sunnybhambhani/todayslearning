## Maximize pod up-time in Kubernetes by utilizing PriorityClass and PodDisruptionBudget ##

In today's world, Kubernetes is the go-to orchestration engine for scheduling workloads and has been widely adopted by all types of organisations, let it be large or small.

In this article, we will go over two important Kubernetes features, "PriorityClass" and "PodDisruptionBudget", and how they can help in making sure that our mission-critical application is highly available and that it is not evicted in case of resource crunch in the cluster.

Assume you have a mission-critical application running on a Kubernetes cluster. This application is made up of multiple pods that are distributed across several cluster nodes. You want to ensure that the application is highly available and that it can continue to function even if one or more nodes fail due to maintenance or other reasons.

You can accomplish this by specifying in a PDB that a certain number of pods should always be available, even during planned disruptions. Set the minimum number of available pods to two, for example, so that if a node fails, the PDB ensures that at least two pods continue to run on other nodes.

Furthermore, you can use a PriorityClass to prioritise this application's criticality over other applications running on the same cluster. This ensures that when resources are scarce, the Kubernetes scheduler prioritises scheduling the pods of this critical application over less critical workloads.

### What are PriorityClass and PodDisruptionBudget? ###

Before we dive more into this article, lets understand both these concepts:

The PodDisruptionBudget (PDB) is a Kubernetes object that specifies the minimum requirements for a set of pods' availability during planned disruptions such as maintenance, upgrades, scaling, or other events that may cause a disruption.

PDBs can be used in a Kubernetes cluster to ensure the high availability of critical workloads. Even during planned disruptions or events, the PDB ensures that a certain number or percentage of pods in a deployment or replica set are always available and running.

PDB prevents the removal of additional pods from service until the minimum availability requirements are met. It adds another layer of reliability and resilience to Kubernetes workloads, ensuring that they are highly available and can continue to operate even when there are disruptions.
