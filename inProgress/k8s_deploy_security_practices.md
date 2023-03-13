 ## Best Practices for Securing Kubernetes Deployments ##
 
Although Kubernetes is a powerful container orchestration system, its complexity makes it a prime target for security attacks. We'll go over some of the best practices for securing the Kubernetes deployments and keeping applications and data safe in this article.

This article is only about pods or deployments; I intend to cover other topics in subsequent articles.

The following is a list of settings and configurations that can be implemented to accomplish the intended goal.

- **securityContext.allowPrivilegeEscalation**
  - The `securityContext.allowPrivilegeEscalation` setting determines whether a container's privileges can be escalated. When `true`, it grants a container additional privileges beyond those granted by default.
  - Setting `allowPrivilegeEscalation` to `false` can help reduce the risk of privilege escalation attacks.
  ```yaml
  spec:
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      allowPrivilegeEscalation: false
  
  # Output trimmed
  ```

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


- **replicas**
  - A replica is a duplicate of a pod that runs a single application. When you deploy an application in Kubernetes, you can use the `replicas` key to specify the number of replicas you want. 
  - This instructs Kubernetes on how many instances of the pod should be running at any given time.
  - In below example we are specifying `replicas` as `3`, which mean `3` identical pods will run on the cluster.
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: webapp
  spec:
    replicas: 3
  
  # Output trimmed
  ```

- **image**
  - When deploying applications in production, the deployment or the pods should specify an `image` tag. It is best to avoid using the `:latest` image tag or no tag.
  - By doing this, it becomes difficult to determine which version of the image is in use and to roll back the version.
  - In below example we are specifying the tag as `1.17` for `nginx` image.
  ```yaml
      containers:
      - name: webapp
        image: nginx:1.17

  # Output trimmed
  ```

- **namespace**
  - Deployments should not be configured with the 'default' namespace; ensure that the `default` namespace is not used.
  - In below example we are specifying namespace as `frontend`, where the application `webapp` will be deployed.
  ```yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: webapp
    namespace: frontend
  spec:
  
  # Output trimmed
  ```

- **securityContext.capabilities**
  - It is recommended that containers drop all capabilities, and only authorized or permitted ones should be added if necessary. 
  - This helps to mitigate the risk of potential privilege escalation attacks on the containers.
  - Set the capabilities field to an empty object `{}` to remove all Linux capabilities from the container.
  ```yaml
      containers:
      - name: webapp
        image: nginx:1.17
        securityContext:
          capabilities: {}
  
  # Output trimmed
  ```
  
- **securityContext.capabilities.add**
  - If required you can use `add` to specify specific capabilities.
  ```yaml
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      capabilities:
        add:
        - SYS_ADMIN
  
  # Output trimmed
  ```

- **securityContext.capabilities.drop**
  - If required you can use `drop` to remove specific capabilities.
  ```yaml
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      capabilities:
        drop:
        - SYS_TIME
  
  # Output trimmed
  ```

> **_NOTE:_**  For more information around capabilities, fire `man capabilities`.




