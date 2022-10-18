Basically, I was attempting to create a workflow in which I needed to retrieve the latest tag (not the name latest, but the actual tag name) for an image that had recently been pushed to a registry, as well as when that image was pushed.

There can be N number of usecases in which this information can be useful, but the top one (based on my usecases) is that we never want to deploy an application with the latest tag. Therefore, there should be a way to find out which is the latest one so that we can persist the image tag and use it as and when required to avoid anamolies.

I assumed this usecase was fairly common and that I would be able to find something like an --orderby flag, etc., but I was unable to find any useful command for it.

</br>
</br>
But later on, I read about the Docker Registry API, and it really helped me a lot. Just to add some of the commands which I found really handy:


- Let's say if you are looking to find out what all the repo's a registry has:
```
curl -X GET -s -u $USERNAME:$PASSWORD https://$REGISTRY/v2/_catalog
```
- Get a list of tags in a particular repo:
```
curl -X GET -s -u $USERNAME:$PASSWORD https://$REGISTRY/v2/$REPO/tags/list
```
- Get the list in pages or limit the output of the above two commands, just append it with the below characters:
```
?n=2
```
- If you want to get detailed information about all the layers when they were created?, What instruction was used per layer?, What is the command or entrypoint?, etc. The below command can be really handy.
```
curl -X GET -s -u $USERNAME:$PASSWORD https://$REGISTRY/v2/$REPO/manifests/$TAG
```

```
Quick information about all the variables and flags that are used here:

$USERNAME: Name of the user by which we need to authenticate, if your docker registry is locally hosted, we can ignore this one.
$PASSWORD: Password of the user by which we need to authenticate.
$REGISTRY: FQDN of the registry. 
$REPO: Specific repository in which we are looking out for details. 
$TAG: Specific tag for which we are looking out for details.
-u: curl's flag for passing username and password.
-s: curl's flag to slient the verbose information.
-X: HTTP request method, here we are doing GET.
```

</br>
I have yet to go into this topic in much detail, since there is a lot that we can do with the Docker Registry APIs.

- Will update this file soon with what all things we can do with this.

</br>
If interested, check this out: https://docs.docker.com/registry/spec/api/
