Setting up Argo CD with Helm [ArcoCD manages itself]
---

[https://www.arthurkoziel.com/setting-up-argocd-with-helm/](https://www.arthurkoziel.com/setting-up-argocd-with-helm/)

1. Install ArgoCD with Helm
---

```bash
kubectl create ns argocd
kubens argocd

export GITHUB_USERNAME=avpatel257
export GITHUB_TOKEN=6270c82e60545256cfb2816005cbb979fede0b61
kubectl create secret generic -n argocd git-credential \
--from-literal=username=${GITHUB_USERNAME} \
--from-literal=password=${GITHUB_TOKEN}

helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

git add charts/argo-cd
git commit -m 'add argo-cd chart'
git push

helm install argo-cd charts/argo-cd/ -n argocd


# Retrieve ArgoCD password
kubectl get secret -n argocd argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d
```


2. Install the root app
---

```bash
helm template apps/ | kubectl apply -n argocd -f -
```

3. Letting ArgoCD manage itself
---

At this point we can remove argo helm release.

```bash
kubectl delete secret -n argocd -l owner=helm,name=argo-cd
```