# kube-prometheus-stack
This is a full implementation of a monitoring system based on the community kube-prometheus-stack helm template. Full documentation on that project is available [here](https://github.com/prometheus-community/helm-charts/tree/main/charts/kube-prometheus-stack)

## Folder Structure
In the base directory we have the base and overlays folders. Items in base are resources that are added without having to modify existing resources. Items in overlays are merged with existing resources (or in the case of delete-grafana-secret.yaml remove default resources from the helm chart).