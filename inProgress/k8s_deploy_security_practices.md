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


- **securityContext.runAsNonRoot**
  - The `securityContext.runAsNonRoot` setting is used to prevent containers from being run as the `root` user, which can be dangerous. 
  - When `runAsNonRoot` is set to `true`, the container is started with a non-root user ID (UID) instead of the default root UID of 0.
  - The `securityContext` section is used in the following example to set `runAsNonRoot` to `true`. This means the container will be started with a non-root UID.
  - It's generally recommended to run containers as non-root whenever possible to reduce the risk of privilege escalation attacks. Keep in mind, however, that some applications may require root access to function properly.
  ```yaml
  spec:
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      runAsNonRoot: true
  
  # Output trimmed
  ```


- **securityContext.readOnlyRootFilesystem**
  - The `securityContext.readOnlyRootFilesystem` setting is used to prevent write access to a container's root filesystem. 
  - When this setting is enabled and set to `true`, the container's root filesystem is mounted as read-only, resulting in a runtime error if any attempt to write to the root filesystem fails.
  - Enabling this will for sure reduce the attack surface, However, keep in mind that this setting may not be appropriate for all containers, particularly those that require write access to the root filesystem to function properly.
  ```yaml
  spec:
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      readOnlyRootFilesystem: true
   
  # Output trimmed
  ```


- **securityContext.runAsUser**
  - The `securityContext.runAsUser` setting in Kubernetes is used to specify the user ID that should be used to run a container. 
  - Containers are run as the `root` user by default, which can pose a security risk if an attacker gains access to the container.
  - To reduce the risk of privilege escalation attacks, containers should be run as non-root users whenever possible.
  - This configuration can be used at the pod and/or container levels; if set at the container level, it will override the pod's configuration.
  ```yaml
  spec:
  securityContext:
    runAsUser: 1000
  containers:
  - name: webapp
    image: nginx:1.17
  
  # Output trimmed
  ```


- **securityContext.runAsGroup**
  - The `securityContext.runAsGroup` setting specifies the group ID under which the container's main process should run.
  - - This configuration too can be used at the pod and/or container levels; if set at the container level, it will override the pod's configuration.
  ```yaml
  spec:
  containers:
  - name: webapp
    image: nginx:1.17
    securityContext:
      runAsGroup: 1000
  
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

- **resources.limits.cpu**
- **resources.limits.memory**
  - The settings `resources.limits.cpu` and `resources.limits.memory` specifies the `maximum` amount of CPU/Memory that a container can use. 
  - It's used to restrict the amount of CPU/Memory resources that a container can use.
  ```yaml
  spec:
  containers:
  - name: webapp
    image: nginx:1.17
    resources:
      limits:
        cpu: "1"
        memory: "512Mi"
  
  # Output trimmed
  ```
  
  
- **resources.requests.cpu**
- **resources.requests.memory**
  - The settings `resources.requests.cpu` and `resources.requests.memory` specifies the `minimum` amount of CPU/Memory that a container should use. 
  - It's used to allocate the amount of CPU/Memory resources on the node for container.
  ```yaml
    containers:
  - name: webapp
    image: nginx:1.17
    resources:
      requests:
        cpu: "0.5"
        memory: "256Mi"
  ```


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



> **_NOTE:_**  
> - For more information around capabilities, fire `man capabilities`.
> - There are many other securityContext options available, but these are the ones that are most commonly used.

