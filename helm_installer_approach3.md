In this article, we will be talking about Approach 3 i.e. how to get multiple helm charts installed using a simple bash utility.

If you haven't read the previous article where I discussed other approaches, feel free to read it over.

- Ref: [Installing multiple helm charts in one go [Approach 1 - using parent/child charts]](https://dev.to/aws-builders/installing-multiple-helm-charts-in-one-go-approach-1-5d1p)
- Ref: [Installing multiple helm charts in one go [Approach 2 - using helmfile]](https://dev.to/aws-builders/installing-multiple-helm-charts-in-one-go-approach-2-using-helmfile-4e4h)

**Motive**

Why I thought of a simple bash utility?

- In some of the air-gapped environments it is sometimes a bit difficult to use the tools/utilities available because moving things inside an air-gapped environment is a challenge.
- Some of the environments are so secure that one may need to follow a whole process of getting all the security clearances and approval before using a tool/utility, which altogether is a nightmare.
- I chose bash, the reason being it is pretty common among engineers and it is easily understandable.
- The source code can be found here: https://github.com/sunnybhambhani/helmister
- You can copy it, and tweak it based on your requirements.


PS: I chose a name for this utility as well `helmister` or `helm-minister`  but you can call it whatever you want 🙂


**Prerequisites**

- A running Kubernetes cluster with proper permissions, here I have used minikube.
- `kubectl` and `helm` installed on your machine to interact with the Kubernetes cluster.
  - Ref: https://kubernetes.io/docs/tasks/tools/
  - Ref: https://helm.sh/docs/intro/install/
- A clone of helmister repository.
- yq(required) and cowsay(optional) packages installed on your machine.


**Helmister**

- This is a small bash utility that can help to install and uninstall multiple helm charts in one go. The idea is inspired by `helmfile`.
- Its usage is quite simple, just run the utility followed by the option like install or uninstall i.e. `./helmister [install/uninstall]`.
- Under the hood, it calls `helm` binary. Therefore it is kind of a wrapper around `helm`.
- It consumes a `config.yaml` file which contains all the necessary details about the releases, and common parameters.
- This supports both `oci://` and `https://` helm registries.
- PS: In the future, I am planning to add some more options/functionalities to this.


**Directory structure**
```
.
├── config.yaml
├── helmister
├── logs
│   ├── archive
│   │   └── helmister_20240227_170201.tar.gz
│   └── helmister.log
├── README.md
└── values
    ├── argo-cd.yaml
    └── nginx-values.yaml
```

- _helmister_, this is the script written in bash you can just cat and see what all things it contains.
- _config.yaml_, this is the main configuration file or you can call the state file which contains the list of all releases you want to install in a cluster, plus it also contains some additional key:value pairs that are generic and common across all the releases. I first kept this configuration file in csv format, but later decided to convert it to yaml because it is more readable.
- _logs_, this is a directory that holds the logs of this utility, it contains the information about the execution of the last iteration, plus any archived/past logs (if required for reference).
- _README.md_, contains bit of a documentation about this utility, and what options are present.
- _values_, this is the directory where all the values files are placed (it can be anywhere in your system but for simplicity, I have placed them in the same directory).
- Now let's talk about `config.yaml` which is the main ingredient.


**config.yaml**

```
dry_run: false
create_namespace: true
wait: false
timeout: false # If true, defaults to 20 mins
charts:
  - release_name: nginx
    chart_name: nginx
    chart_repo: oci://registry-1.docker.io/bitnamicharts
    values_file: values/nginx-values.yaml
  - release_name: argocd
    chart_name: argo-cd
    chart_repo: https://argoproj.github.io/argo-helm
    values_file: values/argo-cd.yaml
    version: 6.4.0
    namespace: argo-cd
```

- The initial key:value pairs are common across all the releases.
- _charts_, is an array that contains a list of what all releases need to be installed in a Kubernetes cluster.
- _dry_run_, it is a boolean [true or false], and if true, none of the Kubernetes resources will be created it will just do a dry_run.
- _create_namespace_, it is a boolean [true or false], and if true it will automatically create a namespace for the release specified in the charts array.
- _wait_, it is a boolean [true or false], and if true it acts similar to `helm --wait` wherein the shell will be kept occupied until all the Kubernetes resources are created.
- _timeout_, it is a boolean [true or false], and if true it acts similar to `helm --timeout=20m` i.e. if all the resources are not created within 20 mins the execution will fail. By default, I have kept the timeout as 20 minutes which is more than enough.
- _charts_, it is an array that contains details around individual releases. Except for version and namespace all the key:value pairs are mandatory.
  - _release_name_, this is the name of the release.
  - _chart_name_, this is the name of the chart that needs to be installed.
  - _chart_repo_, this is the helm registry where the chart is located. It can be any `oci://` or `https://` registry.
  - _values_file_, which contains the path of the values file for individual releases.
  - _version_, this is the version of the chart that needs to be installed. This is optional and if not provided, It will consider the `latest` chart version.
  - _namespace_, this is where the chart will be installed. This is optional as well and if not provided it will be installed in the `default` namespace.


**Let's see this in action**

- I will use the same `config.yaml` which will install one helm release from an `oci://` registry in `default` namespace (since I haven't specified any namespace for that release) and another one from `https://` registry in `argo-cd` namespace. Note the version as well for nginx, there as well I haven't specified any version, it will pick the latest available one automatically.
- This is my cluster's current status.

```
$ k get ns
NAME              STATUS   AGE
default           Active   28s
kube-node-lease   Active   28s
kube-public       Active   29s
kube-system       Active   29s
```
```
$ helm list -A
NAME    NAMESPACE       REVISION        UPDATED STATUS  CHART   APP VERSION
```
```
$ ./helmister install
[2024-07-23 15:02:47] [INFO] Using file: config.yaml
 ____________________________
< Helmister, Install charts! >
 ----------------------------
   \
    \
        .--.
       |o_o |
       |:_/ |
      //   \ \
     (|     | )
    /'\_   _/`\
    \___)=(___/

[2024-07-23 15:02:47] [INFO] Generic/common values based on config.yaml file
[2024-07-23 15:02:48] [INFO] Dry Run: false
[2024-07-23 15:02:48] [INFO] Create Namespace: true
[2024-07-23 15:02:48] [INFO] Wait: false
[2024-07-23 15:02:48] [INFO] Timeout: false
[2024-07-23 15:02:48] [INFO] ****************************
[2024-07-23 15:02:48] [INFO] Chart specific values based on config.yaml file
[2024-07-23 15:02:48] [INFO] Release Name: nginx
[2024-07-23 15:02:48] [INFO] Chart Name: nginx
[2024-07-23 15:02:48] [INFO] Chart Repo: oci://registry-1.docker.io/bitnamicharts
[2024-07-23 15:02:48] [INFO] Values File: values/nginx-values.yaml
[2024-07-23 15:02:48] [INFO] Version: null
[2024-07-23 15:02:48] [INFO] Namespace: null
[2024-07-23 15:02:48] [INFO] ****************************
[2024-07-23 15:02:48] [INFO] Installing nginx with release name nginx from oci://registry-1.docker.io/bitnamicharts with version null using values file: values/nginx-values.yaml in default namespace
...
...
OUTPUT TRIMMED
```

- Actual stdout output can be [found here](https://github.com/sunnybhambhani/helmister/blob/main/artifacts/sample_logs/during_installation_on_stdout.log).
- Here you will see that it provides all the minute details of what exactly it is doing.
- For example: Generic values
  - Here I have marked create_namespace as true because I don't already have argo-cd namespace wherein I want to install argocd release.

```
[2024-07-23 15:02:47] [INFO] Generic/common values based on config.yaml file
[2024-07-23 15:02:48] [INFO] Dry Run: false
[2024-07-23 15:02:48] [INFO] Create Namespace: true
[2024-07-23 15:02:48] [INFO] Wait: false
[2024-07-23 15:02:48] [INFO] Timeout: false
```
- Next, you will see, chart/release specific values of all the items in charts array one by one:

```
[2024-07-23 15:03:31] [INFO] Chart specific values based on config.yaml file
[2024-07-23 15:03:31] [INFO] Release Name: argocd
[2024-07-23 15:03:31] [INFO] Chart Name: argo-cd
[2024-07-23 15:03:31] [INFO] Chart Repo: https://argoproj.github.io/argo-helm
[2024-07-23 15:03:31] [INFO] Values File: values/argo-cd.yaml
[2024-07-23 15:03:31] [INFO] Version: 6.4.0
[2024-07-23 15:03:31] [INFO] Namespace: argo-cd
```
- Here I have explicitly added an additional check that will check if the pods are up and healthy (this is just specific to pods it won't consider any other k8s objects).
- It will continuously check for next 20mins and will check every 5 secs. If the pods are still in non-running state it will terminate the process.

```
[2024-07-23 15:03:47] [WARN] Pods for release argocd are not yet in Running state, checking again in 5 seconds
```

- Once all the pods are in running state and it has deployed all the releases it will show a successful message.

```
[2024-07-23 15:05:20] [INFO] All actions completed successfully
```

- Once this is done, if required you can see the logs as well from the logs directory. A sample can be [found here](https://github.com/sunnybhambhani/helmister/blob/main/artifacts/sample_logs/actual_log_file.log).
- And here is my cluster's current status now:

```
$ helm list -A
NAME    NAMESPACE       REVISION        UPDATED                                 STATUS          CHART           APP VERSION
argocd  argo-cd         1               2024-07-23 15:03:34.46715742 +0530 IST  deployed        argo-cd-6.4.0   v2.10.1
nginx   default         1               2024-07-23 15:02:53.590367521 +0530 IST deployed        nginx-18.1.5    1.27.0
```
```
$ k get ns
NAME              STATUS   AGE
argo-cd           Active   58m
default           Active   62m
kube-node-lease   Active   62m
kube-public       Active   62m
kube-system       Active   62m
```
```
$ k get pods -n argo-cd
NAME                                                READY   STATUS    RESTARTS   AGE
argocd-application-controller-0                     1/1     Running   0          58m
argocd-applicationset-controller-68cd75bc89-d4q8x   1/1     Running   0          58m
argocd-dex-server-84b5bbdcbf-c4qv2                  1/1     Running   0          58m
argocd-notifications-controller-7bc55c495d-xz6gt    1/1     Running   0          58m
argocd-redis-5d5cdcfd54-2rx26                       1/1     Running   0          58m
argocd-repo-server-756ff4cd7d-rpvh7                 1/1     Running   0          58m
argocd-server-7587d49b7b-9h47l                      1/1     Running   0          58m
```
```
$ k get pods -n default
NAME                     READY   STATUS    RESTARTS   AGE
nginx-569b6bc698-48qqt   1/1     Running   0          59m
nginx-569b6bc698-9qzpv   1/1     Running   0          59m
nginx-569b6bc698-wflrj   1/1     Running   0          59m
```

- If you want to uninstall it, simply do:

```
$ ./helmister uninstall
```

- It will get everything cleaned for all the releases that are specified in `config.yaml`.
- The sample log can be [found here](https://github.com/sunnybhambhani/helmister/blob/main/artifacts/sample_logs/during_uninstallation_on_stdout.log).

Feel free to use it, and tweak it based on your requirements.

I will soon add some more functionalities to it.

Happy learning!

References:
- Google/Stackoverflow/Linux man pages/etc
