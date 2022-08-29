Setting up Argo CD with Helm [ArcoCD manages itself]
---

[https://www.arthurkoziel.com/setting-up-argocd-with-helm/](https://www.arthurkoziel.com/setting-up-argocd-with-helm/)


----
```bash
#!/bin/bash
#ArgoCD
kubectl create ns argocd
kubectl config set-context --current --namespace=argocd

export GITHUB_USERNAME=avpatel257
export GITHUB_TOKEN=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
kubectl create secret generic -n argocd git-credential \
--from-literal=username=${GITHUB_USERNAME} \
--from-literal=password=${GITHUB_TOKEN}

sleep 2

# Checkout Argo App Of Apps Repo
git clone git@github.com:avpatel257/argo-poc.git
cd argo-poc

helm repo add argo-cd https://argoproj.github.io/argo-helm
helm dep update charts/argo-cd/

git add charts/argo-cd
git commit -m 'added argo-cd chart'
git push origin main

sleep 1
# Install Argocd with our values
helm install argo-cd charts/argo-cd/

sleep 10

# kubectl get secret argocd-initial-admin-secret -o jsonpath="{.data.password}" | base64 -d

# kubectl port-forward -n argocd svc/argo-cd-argocd-server 9090:80

# Install app of app for nonprod cluster
helm template --set aws.cluster=nonprod app-of-apps/ | kubectl apply -f -

# At this point we can remove argo helm release. Letting ArgoCD manage itself. 
kubectl delete secret -l owner=helm,name=argo-cd

```
