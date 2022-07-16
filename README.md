Setting up Argo CD with Helm [ArcoCD manages itself]
---

[https://www.arthurkoziel.com/setting-up-argocd-with-helm/](https://www.arthurkoziel.com/setting-up-argocd-with-helm/)

1. Install ArgoCD with Helm
---

```bash
helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

git add charts/argo-cd
git commit -m 'add argo-cd chart'
git push

helm install argo-cd charts/argo-cd/

```

2. Install the root app
---

```bash
helm template apps/ | kubectl apply -f -
```

3. Letting ArgoCD manage itself
---

At this point we can remove argo helm release.

```bash
kubectl delete secret -l owner=helm,name=argo-cd
```