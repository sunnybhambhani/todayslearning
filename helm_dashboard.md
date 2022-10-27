# Helm dashboard #

Helm can do a lot, but if we need to further enhance its usability, helm plugins really come in handy.

I was just traversing some articles and came across a plugin called helm dashboard by Komodor which happened to be released just 3 days back at #kubecon , It really looked appealing, so I just thought to give it a try.

PS: If you're interested, you can find some of the plugins here: https://helm.sh/docs/community/related/#helm-plugins 

</br>

>helm plugin [SUB_COMMAND]

is the one which will help you manage all the plugins. 
Ref: https://helm.sh/docs/helm/helm_plugin/

>helm plugin install [PATH/URL]

is the one that will help you install a plugin.
Ref: https://helm.sh/docs/helm/helm_plugin_install/

>helm plugin uninstall [PLUGIN]

is the one by which you can uninstall a plugin.
Ref: https://helm.sh/docs/helm/helm_plugin_uninstall/

</br>

**How this helps:**
1. It gives a UI to see what all helm charts are installed. 
2. It helps to see its revision history. 
4. Graphically browse all the resources that were a part of the helm chart. 
5. It automatically uses KUBECONFIG, so we do not need to make any extra efforts. 
6. You can see all of your helm repositories and add them directly from the UI.
7. It helps to see which charts are part of a repository. 
8. Install the chart from the UI itself. 
9. You can see the k8s manifest outcome on the UI itself and cross check it before installing the chart. 
10. Add user-defined values.
11. It helps in troubleshooting as well, you can check the charts difference from the UI itself.

</br>

Its installation is relatively simple, 

>helm plugin install https://github.com/komodorio/helm-dashboard.git

Once the plugin is installed, just fire:

>helm dashboard 

It will open a local browser where you can traverse the UI and see what all magic it can do and how useful it is.

For more details, check this out: https://github.com/komodorio/helm-dashboard 
