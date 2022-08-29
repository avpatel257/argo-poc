Setting up Argo CD with Helm [ArcoCD manages itself]
---

[https://www.arthurkoziel.com/setting-up-argocd-with-helm/](https://www.arthurkoziel.com/setting-up-argocd-with-helm/)

1. Install ArgoCD with Helm
---

```bash
# kubectl create ns argocd
# kubens argocd

export GITHUB_USERNAME=avpatel257
export GITHUB_TOKEN=6270c82e60545256cfb2816005cbb979fede0b61
kubectl create secret generic git-credential \
--from-literal=username=${GITHUB_USERNAME} \
--from-literal=password=${GITHUB_TOKEN}

helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

git add charts/argo-cd
git commit -m 'add argo-cd chart'
git push

helm install argo-cd charts/argo-cd/


# Retrieve ArgoCD password
kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```


2. Install the root app
---

```bash
helm template argo-root-app/ | kubectl apply -f -
```

3. Letting ArgoCD manage itself
---

At this point we can remove argo helm release.

```bash
kubectl delete secret -l owner=helm,name=argo-cd
```



----
#!/bin/bash

#ArgoCD
kubectl create ns argocd
kubectl config set-context --current --namespace=argocd

export GITHUB_USERNAME=avpatel257
export GITHUB_TOKEN=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
kubectl create secret generic -n argocd git-credential \
--from-literal=username=${GITHUB_USERNAME} \
--from-literal=password=${GITHUB_TOKEN}


# Checkout Argo App Of Apps Repo
git clone git@github.com:avpatel257/argo-poc.git
cd argo-poc

helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

git add charts/argo-cd
git commit -m 'added argo-cd chart'
git push origin main

# Install Argocd with our values
helm install argo-cd charts/argo-cd/

# Install app of app for nonprod cluster
helm template --set aws.cluster=nonprod app-of-apps/ | kubectl apply -f -

# At this point we can remove argo helm release. Letting ArgoCD manage itself. 
kubectl delete secret -l owner=helm,name=argo-cd
