# Argo-CD

[Argo-cd](https://argo-cd.readthedocs.io/en/stable/) helps keep our kubernetes cluster in sync with this repository. Anytime a change is made to this repo, argocd will take those changes and apply them to the cluster in order to keep it in sync. This gives us the bennifit of having versioning, records, and consistent deployments.

# Setup

1. (Optional) If you want to enable ingress, change the url in base/argocd-ingress.yaml
2. (Optional) If you want to add private repos, add a credential template into the repos folder and add it to your kustomization.yaml
