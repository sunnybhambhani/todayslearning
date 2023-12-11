In this article we will be talking about `Approach 2` i.e. how to get multiple helm charts installed using helmfile.

If you haven't read the previous article where I discussed about `Approach 1`, feel free to read it over.

>Ref: https://dev.to/aws-builders/installing-multiple-helm-charts-in-one-go-approach-1-5d1p

### Prerequisites:
- A running Kubernetes cluster with proper permissions, here I have used minikube.
- kubectl, helm, and helmfile installed on your machine to interact with the Kubernetes cluster.
  - Ref: https://kubernetes.io/docs/tasks/tools/
  - Ref: https://helm.sh/docs/intro/install/
  - Ref: https://github.com/helmfile/helmfile


Installation of helmfile is pretty straight forward, we just need to grab its binary and put that in /usr/local/bin and we are done (Ref: https://helmfile.readthedocs.io/en/latest/#installation for operating system other than linux).

Grab the latest one from the assets section from here: https://github.com/helmfile/helmfile/releases.
```bash
sudo wget https://github.com/helmfile/helmfile/releases/download/v0.159.0/helmfile_0.159.0_linux_amd64.tar.gz
sudo tar -xxf helmfile_0.159.0_linux_amd64.tar.gz
sudo rm helmfile_0.159.0_linux_amd64.tar.gz
sudo mv helmfile /usr/local/bin/
```

Once helmfile is installed, we need to initialize it so that it can download necessary helm plugins.
```bash
helmfile init
```

If you want to list it out what all plugins init installs, it can be found using below command:
```bash
$ helm plugin list
NAME            VERSION DESCRIPTION
diff            3.8.1   Preview helm upgrade changes as a diff
helm-git        0.12.0  Get non-packaged Charts directly from Git.
s3              0.14.0  Provides AWS S3 protocol support for charts and repos. https://github.com/hypnoglow/helm-s3
secrets         4.1.1   This plugin provides secrets values encryption for Helm charts secure storing
```

> #### Note: A notice to upgrade helm may appear if the helm version is 3.10 or lower.
>
> helm version is too low, the current version is 3.10.1+g9f88ccb, the required version is 3.12.3
use: 'https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3' [y/n]: y


### Approach 2:

helmfile is pretty intresting and a very useful utility when it comes to get multiple helm charts installed. Using a single file `helmfile.yaml` we can manage N number of charts.

When it comes to installing mutiple helm charts, helmfile is a really helpful and interesting tool. The `helmfile.yaml` values file and helmfile binary is all that is needed to manage N number of charts. Internally helmfile invokes helm command to manage Kubernetes objects/charts. Also you can think of helmfile as a wrapper around helm together with some helm plugins.

Below is a quick example, wherein I have a 3-Tier application comprising of a frontend, backend, and database and for all of them I have 3 individual charts.

```bash
$ tree
.
├── backend
│   ├── Chart.yaml
│   ├── templates
│   │   ├── _helpers.tpl
│   │   ├── deployment.yaml
│   │   ├── hpa.yaml
│   │   ├── ingress.yaml
│   │   ├── service.yaml
│   │   └── serviceaccount.yaml
│   └── values.yaml
├── database
│   ├── Chart.yaml
│   ├── templates
│   │   ├── _helpers.tpl
│   │   ├── deployment.yaml
│   │   ├── hpa.yaml
│   │   ├── ingress.yaml
│   │   ├── service.yaml
│   │   └── serviceaccount.yaml
│   └── values.yaml
└── webapp
    ├── Chart.yaml
    ├── templates
    │   ├── _helpers.tpl
    │   ├── deployment.yaml
    │   ├── hpa.yaml
    │   ├── ingress.yaml
    │   ├── service.yaml
    │   └── serviceaccount.yaml
    └── values.yaml

6 directories, 24 files
```

Now, we will use helmfile (that too a single command to get these charts installed).

In the same directory where my charts are there, lets create a new file called `helmfile.yaml`. Just to add here we are referring local charts now, but we will refer charts from helm repositories in next examples.

```bash
$ cat helmfile.yaml
releases:
  - name: webapp
    namespace: default
    chart: ./webapp
    version: "0.1.0"
    wait: true
    installed: true
  - name: backend
    namespace: default
    chart: ./backend
    wait: true
  - name: database
    namespace: default
    chart: ./database
    wait: true
```

Lets break this file now:
- There can be multiple sections in this `YAML` file, but majorly we will focus on `.releases`, `.repositories`, and `.helmDefaults` sections.
- For more details around what all things we can have in a helmfile.yaml, ref: https://helmfile.readthedocs.io/en/latest/#configuration.
- `.releases` is an array which basically signifies what all helm releases we need to deploy in a Kubernetes cluster. 
  - Important KV pairs in each item are `.releases[].name` which refers to the name of the release.
  - `.releases[].namespace`, it refers to the name of the namespace where we need to install the chart.
  - `.releases[].chart`, it refers to the location of the chart from where helm needs to pull it. In this example its in my local machine and in the current directory.
  - `.releases[].version`, it refers to the version of chart which needs to be installed.
  - `.releases[].installed`, it is a boolean key which referes if we want to get specific chart installed or not (by default the value is true).
  - `.releases[].wait`, it is same as `--wait` flag of helm, which means the operation will wait untill all the components are ready/healthy. Ref: https://helm.sh/docs/intro/using_helm/.

Now, lets see helmfile in action:
- As of now nothing is installed. 
```bash
$ helm list -A
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
$
```
- We will first initialize helm to gather all the dependencies.
```bash
$ helmfile init
helmfile initialization completed!
$
```
- Lets list out what all releases are defined in our release file?
```bash
$ helmfile list
NAME            NAMESPACE       ENABLED INSTALLED       LABELS  CHART           VERSION
webapp          default         true    true                    ./webapp        0.1.0
backend         default         true    true                    ./backend
database        default         true    true                    ./database
$
```
- All, looks good, lets apply the state file using `helmfile apply`.
```bash
$ helmfile apply
Building dependency release=webapp, chart=webapp
Building dependency release=backend, chart=backend
Building dependency release=database, chart=database
Comparing release=webapp, chart=webapp
********************

        Release was not present in Helm.  Diff will show entire contents as new.

********************
default, webapp, Deployment (apps) has been added:
-
+ # Source: webapp/templates/deployment.yaml
+ apiVersion: apps/v1

#.........
#.........
# OUTPUT TRIMMED

UPDATED RELEASES:
NAME       CHART        VERSION   DURATION
webapp     ./webapp     0.1.0           3s
database   ./database   0.1.0           3s
backend    ./backend    0.1.0           3s
```
- If you will see here, using a single command it instructed helm to build the dependency of all the charts specified in the state file then it used `helm diff` to list out actual k8s yaml files, then it installs all the releases.
- If you will fire `helm list` or `kubectl get pods` now, you will see all the intended resources.
```bash
$ helm list -A
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
backend         default         1               2023-12-11 16:13:24.769966852 +0530 IST deployed        backend-0.1.0   1.16.0
database        default         1               2023-12-11 16:13:24.771955874 +0530 IST deployed        database-0.1.0  1.16.0
webapp          default         1               2023-12-11 16:13:24.771767015 +0530 IST deployed        webapp-0.1.0    1.16.0
$ k get pods
NAME                       READY   STATUS    RESTARTS   AGE
backend-7f458d4566-zwcz2   1/1     Running   0          3m47s
database-b4f679788-z6r84   1/1     Running   0          3m47s
webapp-7f6ffdc676-g5w7j    1/1     Running   0          3m47s
$
```
- Get the status using `helmfile status` if reuqired.
```bash
$ helmfile status
Getting status backend
Getting status webapp
Getting status database
NAME: backend
LAST DEPLOYED: Mon Dec 11 16:13:24 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

NAME: webapp
LAST DEPLOYED: Mon Dec 11 16:13:24 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None

NAME: database
LAST DEPLOYED: Mon Dec 11 16:13:24 2023
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
- Now, lets assume you just need to update the database release and that too just the replicaCount, 
  - How to update that using `values` file or using `set` key?
  - What helmfile will do?
  - How to check what all changes helmfile will make?

By default, similar to `helm`, this will also use helm chart's default values.yaml file which is a part of helm chart itself. So either you can update that and fulfill your requirement (which idelly is not a good practice, since in real world scenario that is most unlikely to happen and ideally we should to override indented keys).

To cater this need we have something called `values` and `set` which we can specify in the state file and helmfile will consider those values and will override them.

Lets use the same example, but now I have added few additional line for backend as well as for the database item in our releases array.
```bash
releases:
  - name: webapp
    namespace: default
    chart: ./webapp
    version: "0.1.0"
    wait: true
    installed: true
  - name: backend
    namespace: default
    chart: ./backend
    wait: true
    set:
      - name: replicaCount
        value: 2
  - name: database
    namespace: default
    chart: ./database
    wait: true
    values:
      - "./db-values.yaml"
```

```bash
$ cat db-values.yaml
replicaCount: 2
```
- Just to cut short, `values` is same as `--values` and `set` is same as `--set` in helm.
- Here if you will see, for `backend` I have used `set` and I am overwriding `relicaCount` to `2` and for `database` I have used 'values' and there I have passed a path where my values file resides which contains again the `replicaCount` as `2` but for database release.
- Now, comes the 2nd question, how to see what all differences will helmfile make, simply fire `helmfile diff`.
```bash
$ helmfile diff
Building dependency release=webapp, chart=webapp
Building dependency release=backend, chart=backend
Building dependency release=database, chart=database
Comparing release=webapp, chart=webapp
Comparing release=backend, chart=backend
default, backend, Deployment (apps) has changed:
  # Source: backend/templates/deployment.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: backend
# ......
# OUTPUT TRIMMED

-   replicas: 1
+   replicas: 2
# ......
# OUTPUT TRIMMED

Comparing release=database, chart=database
default, database, Deployment (apps) has changed:
  # Source: database/templates/deployment.yaml
  apiVersion: apps/v1
  kind: Deployment
  metadata:
    name: database
# ......
# OUTPUT TRIMMED

-   replicas: 1
+   replicas: 2
# ......
# OUTPUT TRIMMED
```
- Here, if you will observe, firstly it compared the release webapp (wherein it didn't found any differences then it compared backend where it found that the replicaCount was updated to `2`) then it compared database where it again found that the replicaCount was updated to `2`.
- Using `helmfile apply`, we will apply the changes now.
> **NOTE**: `helmfile apply` basically calls two operations `helmfile diff` and `helmfilesync`.
- Again it will show you what all changes it will bring then will apply the changes.
```bash
$ helmfile apply
Building dependency release=webapp, chart=webapp
Building dependency release=backend, chart=backend
Building dependency release=database, chart=database
Comparing release=webapp, chart=webapp
default, backend, Deployment (apps) has changed:
# ..........
# OUTPUT TRIMMED

-   replicas: 1
+   replicas: 2

default, database, Deployment (apps) has changed:
# ..........
# OUTPUT TRIMMED

-   replicas: 1
+   replicas: 2

Upgrading release=database, chart=database
Upgrading release=backend, chart=backend
Release "backend" has been upgraded. Happy Helming!
NAME: backend
LAST DEPLOYED: Mon Dec 11 16:37:27 2023
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None

Listing releases matching ^backend$
Release "database" has been upgraded. Happy Helming!
NAME: database
LAST DEPLOYED: Mon Dec 11 16:37:27 2023
NAMESPACE: default
STATUS: deployed
REVISION: 2
TEST SUITE: None

UPDATED RELEASES:
NAME       CHART        VERSION   DURATION
backend    ./backend    0.1.0           3s
database   ./database   0.1.0           3s
```

```bash
$ helm list
NAME            NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
backend         default         2               2023-12-11 16:37:27.366097438 +0530 IST deployed        backend-0.1.0   1.16.0
database        default         2               2023-12-11 16:37:27.366586959 +0530 IST deployed        database-0.1.0  1.16.0
webapp          default         1               2023-12-11 16:13:24.771767015 +0530 IST deployed        webapp-0.1.0    1.16.0
```
- Revision got incremented by `1`, and now I have `2` new pods running +1 for backend and +1 for database.
```bash
$ k get pods
NAME                       READY   STATUS    RESTARTS   AGE
backend-7f458d4566-6jgxk   1/1     Running   0          12s
backend-7f458d4566-zwcz2   1/1     Running   0          24m
database-b4f679788-9kxhl   1/1     Running   0          12s
database-b4f679788-z6r84   1/1     Running   0          24m
webapp-7f6ffdc676-g5w7j    1/1     Running   0          24m
```

