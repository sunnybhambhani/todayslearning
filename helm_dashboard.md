Helm can do a lot but if in case we need to further enhance its usability, helm plugins really comes handy.

I was just traversing some articles and came across a plugin called helm dashboard by Komodor which happens to be release just 2 days back, It really looked appealing so just thought to give it a try.

PS: BTW if intrested, you can find some of the plugins here: https://helm.sh/docs/community/related/#helm-plugins 

helm plugin [SUB_COMMAND], is the one which will help you to manage all the plugins.
Ref: https://helm.sh/docs/helm/helm_plugin/

helm plugin install [PATH/URL], is the one which will help you to install a plugin.
Ref: https://helm.sh/docs/helm/helm_plugin_install/

Similarly for uninstalling, you can use helm plugin uninstall [PLUGIN].
Ref: https://helm.sh/docs/helm/helm_plugin_uninstall/


How this helps:
1. It gives a UI to see what all helm charts are installed.
2. Helps to see its revision history.
3. Graphically browse all the resources which were a part of helm chart.
4. It automatically uses KUBECONFIG, so we need to not do any extra efforts.


Its installation is relatively simple, 

helm plugin install https://github.com/komodorio/helm-dashboard.git

Once the plugin is installed, just fire:

helm dashboard 

