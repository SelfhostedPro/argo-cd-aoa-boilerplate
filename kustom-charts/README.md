# Kustom-charts
This folder will hold all [kustomized-helm](https://github.com/kubernetes-sigs/kustomize/blob/master/examples/chart.md) charts. This allows for easy deployment of applications with the ability to kustomize them.


This is the command that's run on the argocd side of things to deploy these apps.
```bash
kustomize build --enable-helm 
```
This runs the equivalent of the helm template command, makes changes to the output, and applys the result. This is essential for things like removing the default password for grafana, ldap settings, and using sealed-secrets.