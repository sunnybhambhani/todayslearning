 ## Best Practices for Securing Kubernetes Deployments ##
 
Although Kubernetes is a powerful container orchestration system, its complexity makes it a prime target for security attacks. We'll go over some of the best practices for securing the Kubernetes deployments and keeping applications and data safe in this article.

- securityContext.allowPrivilegeEscalation
- securityContext.capabilities.drop
- securityContext.runAsNonRoot
- securityContext.readOnlyRootFilesystem
- securityContext.runAsUser
- securityContext.runAsGroup
- securityContext.seccompProfile.type
- resources.limits.cpu
- resources.limits.memory
- resources.requests.cpu
- resources.requests.memory


- replicas
  - A replica is a duplicate of a pod that runs a single application. When you deploy an application in Kubernetes, you can use the replicas key to specify the number of replicas you want. 
  - This instructs Kubernetes on how many instances of the pod should be running at any given time.
  - In below example we are specifying `replicas` as `3`, which mean `3` identical pods will run on the cluster.
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: webapp
  spec:
    replicas: 3
  
  # Output trimmed
  ```

- image
  - When deploying applications in production, the deployment or the pods should specify an image tag. It is best to avoid using the `:latest` image tag or no tag.
  - By doing this, it becomes difficult to determine which version of the image is in use and to roll back the version.
  - In below example we are specifying the tag as `1.17` for `nginx` image.
  ```
      containers:
      - name: webapp
        image: nginx:1.17

  # Output trimmed
  ```

- namespace
  - Deployments should not be configured with the 'default' namespace; ensure that the default namespace is not used.
  - In below example we are specifying namespace as `frontend`, where the application `webapp` will be deployed.
  ```
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: webapp
    namespace: frontend
  spec:
  
  # Output trimmed
  ```

- securityContext.capabilities
  - It is recommended that containers drop all capabilities, and only authorized or permitted ones should be added if necessary. 
  - This helps to mitigate the risk of potential privilege escalation attacks on the containers.
  - Set the capabilities field to an empty object {} to remove all Linux capabilities from the container.
  ```
      containers:
      - name: webapp
        image: nginx:1.17
        securityContext:
          capabilities: {}
  
  # Output trimmed
  ```






