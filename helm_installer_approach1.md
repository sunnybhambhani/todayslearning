Helm is really a very powerful tool for managing `Kubernetes` objects and is widely adopted across all kinds of organizations. It is truely a game changer on how Kubernetes objects are being managed. 

With a single command we can install or upgrade multiple related Kubernetes entities together, we need not to worry about how the resources will be created, Helm will do all the heavy lifting for us.

```bash
$ helm install [RELEASE_NAME] [REPO]/[CHART]
```

But below post is not about the basics of helm or how to install or upgrade a chart, etc. It's about some new ways of getting charts deployed.

Let's say you have a requirement for getting more than 100 charts installed on a specific Kubernetes cluster, what would you do in this scenario?

On top of my head I can think of couple of approaches:


### Prerequisites:
- A running Kubernetes cluster with proper permissions, here I have used minikube.
- kubectl and helm installed on your machine to interact with the Kubernetes cluster.
  - Ref: https://kubernetes.io/docs/tasks/tools/
  - Ref: https://helm.sh/docs/intro/install/

### Approach 1:

Club inter-related charts into one, as in get a parent/wrapper chart created which will inturn have the related child charts as dependency. Which mean if we install the parent/wrapper chart it will get all the Kubernetes resources of all the child charts deployed (provided they are marked as enabled in Chart.yaml).

Below is a quick example, wherein I have a 3-Tier application named app01.
- If you notice the output of both the commands in parent's Chart.yaml I have all three charts listed as dependencies and in the other one, individual charts have its own Kubernetes resources.

```bash
$ cat Chart.yaml
apiVersion: v2
name: app01
description: A Helm chart is for app01 which contains FE, BE, DB.
version: 0.1.0
appVersion: 1.0.0
dependencies:
  - name: webapp
    version: "0.1.0"
    repository: "file://./charts/webapp"
    enabled: true
  - name: backend
    version: "0.1.0"
    repository: "file://./charts/backend"
    enabled: true
  - name: database
    version: "0.1.0"
    repository: "file://./charts/database"
    enabled: true
```

```bash
$ tree
.
├── Chart.yaml
├── charts
│   ├── backend
│   │   ├── Chart.yaml
│   │   ├── charts
│   │   ├── templates
│   │   │   ├── _helpers.tpl
│   │   │   ├── deployment.yaml
│   │   │   ├── hpa.yaml
│   │   │   ├── ingress.yaml
│   │   │   ├── service.yaml
│   │   │   └── serviceaccount.yaml
│   │   └── values.yaml
│   ├── database
│   │   ├── Chart.yaml
│   │   ├── charts
│   │   ├── templates
│   │   │   ├── _helpers.tpl
│   │   │   ├── deployment.yaml
│   │   │   ├── hpa.yaml
│   │   │   ├── ingress.yaml
│   │   │   ├── service.yaml
│   │   │   └── serviceaccount.yaml
│   │   └── values.yaml
│   └── webapp
│       ├── Chart.yaml
│       ├── charts
│       ├── templates
│       │   ├── _helpers.tpl
│       │   ├── deployment.yaml
│       │   ├── hpa.yaml
│       │   ├── ingress.yaml
│       │   ├── service.yaml
│       │   └── serviceaccount.yaml
│       └── values.yaml
└── values.yaml

10 directories, 26 files
```

- When we will install the parent chart it will get all the Kubernetes resources deployed.

```bash
$ helm install app01 ./app01/
NAME: app01
LAST DEPLOYED: Sat Dec  2 00:14:50 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
```

```bash
$ helm list -A
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
app01   default         1               2023-12-02 00:14:50.075444065 +0530 IST deployed        app01-0.1.0     1.0.0
```

```bash
$ k get all
NAME                                  READY   STATUS    RESTARTS   AGE
pod/app01-backend-965cb9b5b-q7gqv     1/1     Running   0          17m
pod/app01-database-56bdbcd49c-bh4s2   1/1     Running   0          17m
pod/app01-webapp-5b67db8bdf-m6psg     1/1     Running   0          17m

NAME                     TYPE        CLUSTER-IP       EXTERNAL-IP   PORT(S)   AGE
service/app01-backend    ClusterIP   10.107.53.8      <none>        8080/TCP  17m
service/app01-database   ClusterIP   10.104.173.124   <none>        3306/TCP  17m
service/app01-webapp     ClusterIP   10.111.168.18    <none>        80/TCP    17m
service/kubernetes       ClusterIP   10.96.0.1        <none>        443/TCP   8d

NAME                             READY   UP-TO-DATE   AVAILABLE   AGE
deployment.apps/app01-backend    1/1     1            1           17m
deployment.apps/app01-database   1/1     1            1           17m
deployment.apps/app01-webapp     1/1     1            1           17m

NAME                                        DESIRED   CURRENT   READY   AGE
replicaset.apps/app01-backend-965cb9b5b     1         1         1       17m
replicaset.apps/app01-database-56bdbcd49c   1         1         1       17m
replicaset.apps/app01-webapp-5b67db8bdf     1         1         1       17m
```

- Another thing to note here is now we have 4 values.yaml files (one for each child chart and one for parent helm chart).
- And the good part here is using just one parent's values.yaml file we can manage all 3 of them.
- Make sure in parent's values.yaml, child chart's values are indented correctly and they are indented under specific chart name as mentioned in Chart.yaml under ``.dependencies[*].name``

```bash
$ cat values.yaml
webapp:
  replicaCount: 2

database:
  replicaCount: 1

backend:
  replicaCount: 3
```

- Here we are just updating the replicaCount of all child charts and are upgrading the release.

```bash
$ helm upgrade --install app01 ./app01/
Release "app01" has been upgraded. Happy Helming!
NAME: app01
LAST DEPLOYED: Sat Dec  2 01:21:56 2023
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None
```

- As specified in parent chart values.yaml, the replicaCount is now increased to 2,3 respectively.
```bash
$ k get deploy; k get pods
NAME             READY   UP-TO-DATE   AVAILABLE   AGE
app01-backend    3/3     3            3           105m
app01-database   1/1     1            1           105m
app01-webapp     2/2     2            2           105m
NAME                              READY   STATUS    RESTARTS   AGE
app01-backend-965cb9b5b-dg6gq     1/1     Running   0          38m
app01-backend-965cb9b5b-q7gqv     1/1     Running   0          105m
app01-backend-965cb9b5b-z92c8     1/1     Running   0          38m
app01-database-56bdbcd49c-bh4s2   1/1     Running   0          105m
app01-webapp-5b67db8bdf-c25kc     1/1     Running   0          38m
app01-webapp-5b67db8bdf-m6psg     1/1     Running   0          105m
```

- As you see here with a single command we deployed multiple helm charts and using a single values.yaml file we have the ability to manage all of them.



### Approach 2:
Using helmfile.

### Approach 3:
Using bash or any other scripting language (I am planning to write a simple utility to accomplish this).

Further, I am in a process of writing another articles around Approach 2 and 3, plus there can be multiple other approaches for getting this accomplished (feel free to share your thoughts and comments).

Happy learning :)