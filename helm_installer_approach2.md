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
    wait: true
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
- There are multiple sections in this `YAML` file, but majorly we will focus on `.releases` and `.helmDefaults` sections.
- For more details around what all things we can have in a helmfile.yaml, ref: https://helmfile.readthedocs.io/en/latest/#configuration.
