# Argo CD

## Install Argo CD

```bash
helm repo add argo https://argoproj.github.io/argo-helm
helm repo update
ARGO_VERSION=7.7.5
helm upgrade --install argo-cd argo/argo-cd --version ${ARGO_VERSION} --namespace argocd --create-namespace
```

## Bootstrap Argo CD

```bash
kubectl port-forward service/argo-cd-argocd-server -n argocd 8080:443 > /dev/null 2>&1 &
PASSWORD=$(kubectl get secret argocd-initial-admin-secret -n argocd -o jsonpath="{.data.password}" | base64 -d)
argocd login localhost:8080 --username admin --password ${PASSWORD} --insecure
kubectl apply -f bootstrap/repos.yaml
argocd app create argo-cd --repo https://github.com/gptparv/engg-tools.git --path argo-cd/bootstrap --dest-namespace argocd --dest-server https://kubernetes.default.svc --directory-recurse --sync-policy automated --self-heal 
argocd app wait argo-cd
```