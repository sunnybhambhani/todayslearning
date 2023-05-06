## Manage existing resources via Helm ##

Helm is really a very powerful tool for managing Kubernetes objects. With a single command, we can install or upgrade multiple related Kubernetes entities. Let's say we want to install any chart from the helm registry, we just need to type-in below command and helm will do the heavy lifting for us.

```
$ helm install [RELEASE_NAME] [REPO]/[CHART]
```

But below post is not about the basics of helm or how to install or upgrade a chart, etc. It's been a long time since I wrote anything, so I just thought to write something about what new I learned today :) 

While installing one of my charts, I encountered below issue:

>Error: INSTALLATION FAILED: rendered manifests contain a resource that already exists. Unable to continue with install: StorageClass "demo-sc" in namespace "" exists and cannot be imported into the current release: invalid ownership metadata; label validation error: missing key "app.kubernetes.io/managed-by": must be set to "Helm"; annotation validation error: missing key "meta.helm.sh/release-name": must be set to "demo-release"; annotation validation error: missing key "meta.helm.sh/release-namespace": must be set to "default"

<br />
The cause of the issue is pretty self-explanatory, the storageClass which is a part of the helm chart already exists with identical properties and therefore installation of the chart failed.

And it can be easily fixed by deleting that object [storageClass in my case] and further proceeding with the chart's installation.

**But what exactly happens in the background? how does helm know which object to manage? which object to club together as a part of the release?**

<br />

The answer is simple labels and annotations, if we read the error carefully it gives us a whole lot of hints about what all things helm is expecting in a Kubernetes manifest. 

<br />

> label validation error: missing key "app.kubernetes.io/managed-by": must be set to "Helm";

This is used for finding all the entities managed by helm, if we want to rectify it manually we can just add a label and we are done.

<br />

> annotation validation error: missing key "meta.helm.sh/release-name": must be set to "demo-release";

This is used to identify what all Kubernetes objects are a part of the release [demo-release in my case].

<br />

> annotation validation error: missing key "meta.helm.sh/release-namespace": must be set to "default"

This is used to identify in which namespace the Kubernetes objects must be created [default in my case].

<br />

So, ideally, if we add these three things to an existing object [storageclass in my case] and try again to install the release, helm will treat that object as its baby and will start taking care of it together with other objects of the same release.

<br />

If interested below is a quick HowTo. 

``` 
$ kubectl label sc demo-sc app.kubernetes.io/managed-by=Helm

$ kubectl annotate sc demo-sc meta.helm.sh/release-name=demo-release

$ kubectl annotate sc demo-sc meta.helm.sh/release-namespace=default 
```


*sc/storageClass is the kind of object.\
*demo-sc is the name of the object.

<br />

Once the above commands are executed and now when we will try installing or even uninstalling the chart it will treat the storageClass as a part of that release.

<br />

```
$ helm upgrade --install demo-release ./demo
Release "demo-release" does not exist. Installing it now.
NAME: demo-release
LAST DEPLOYED: Tue Oct 11 11:18:15 2022
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```
