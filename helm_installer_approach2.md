In this article we will be talking about `Approach 2` i.e. how to get multiple helm charts installed using helmfile.

If you haven't read the previous article where I discussed about `Approach 1`, feel free to read it over.

>Ref: https://dev.to/aws-builders/installing-multiple-helm-charts-in-one-go-approach-1-5d1p

### Prerequisites:
- A running Kubernetes cluster with proper permissions, here I have used minikube.
- kubectl, helm, and helmfile installed on your machine to interact with the Kubernetes cluster.
  - Ref: https://kubernetes.io/docs/tasks/tools/
  - Ref: https://helm.sh/docs/intro/install/
  - Ref: https://github.com/helmfile/helmfile


### Approach 2:

helmfile is pretty intresting and a very useful utility when it comes to get multiple helm charts installed. Using a single file `helmfile.yaml` we can manage n number of charts.

When it comes to installing mutiple helm charts, helmfile is a really helpful and interesting tool. The `helmfile.yaml` values file and helmfile binary is all that is needed to manage N number of charts. Internally helmfile invokes helm command to manage Kubernetes objects/charts.

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