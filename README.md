# Argo-cd App of Apps Boilerplate

This repo is a boilerplate to get argo-cd up and running in a self-managed state quickly and easily with a private github repo. (also works for public repos)

## Setup

### Requirments on local machine

- [kubectl](https://kubernetes.io/docs/tasks/tools/) - Kubernetes cli tool
- [kustomize](https://kustomize.io) - Templating utility for kubernetes manifests
- [kubeseal](https://github.com/bitnami-labs/sealed-secrets#homebrew) - Utility for generating sealed secrets

### Public Repo Changes Needed:

When using a public repo we're not going to need to setup sealed secrets (At least for the core argo-cd setup). You'll want to make the following changes:

1. Delete the following files/folders:
   ```bash
   apps/templates/sealed-secrets.yaml
   kustom/sealed-secrets
   kustom/argo-cd/base/argocd-repo-key.yaml
   ```

2. Then change `apps/kustomization.yaml` and remove the following line:
   ```yaml
   - templates/sealed-secrets.yaml
   ```

3. Finally change `kustom/argo-cd/kustomization.yaml` and remove the following line:
  ```yaml
   - base/argocd-repo-key.yaml
  ```
  
**End of public repo setup**

### Deployment

1. Fork this repository.
2. Change all of the git refrences to match your repository:

   git urls are located in the following files:

   ```bash
   apps/templates/*
   kustom/argo-cd/base/argocd-cm
   ```

3. Spin up a fresh kubernetes cluster and then clone your forked repository to your local machine.

4. Open a shell in the cloned repo and run the following command to initialize argo-cd:
   ```bash
   kustomize build kustom/argo-cd/ | kubectl apply -n argocd -f -
   ```

5. Run the following command to get the argo password:
   ```bash
   kubectl -n argocd get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
   ```
   
6. Port forward the webui to your local port 8080:
   ```bash
   kubectl port-forward svc/argocd-server -n argocd 8080:443
   ```
   
7. Open the argo-cd ui, login, and click on the settings cog in the left bar. Add your forked github repo and generate a new ssh key that your're going to be using (make sure you've added it to your github account). Once finished, click on connect.

8. Now that the repo credentials have been added to argo-cd we're going to add our apps to argo-cd with the following command:
   ```bash
   kustomize build apps/ | kubectl -n argocd apply -f -
   ```

9. You should see the apps appear in the argo-cd UI.

The argo-cd app will encounter a sync error if you're using a private repo, this is normal. We're going to generate a sealed secret in order to give argo-cd access to the repo for that app. (this sealed secret is going to be a private key with read access to our git repo, it's safe to put into the repo because it's been asymmetrically encrypted with a key stored on the kubernetes cluster)

10. Once the sealed-secrets app is synced you're going to copy the secret that was created when we initially added the repo via the UI above (should be in the argocd namespace with a name similar to
    repo-1340168060). Save this as key.yaml.
    ```
    kubectl get secret -n argocd repo-1340168060 -o yaml > key.yaml
    ```

11. Run the following command to generate a sealed key and then move it to the right location with the right name:
    ```
    kubeseal --controller-namespace sealed-secrets --format yaml < key.yaml > sealedkey.yaml
    mv sealedkey.yaml kustom/argo-cd/base/argocd-repo-key.yaml
    ```
    
12. Change the name key's value in kustom/argo-cd/base/argocd-repo-key.yaml to argo-github-key

13. Commit this change and push it to the repo in order for argo to finish the setup for argo-cd to manage the cluster and itself.
