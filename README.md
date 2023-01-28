# argo-workflows-newbie

## Usage

- Create new OAuth app
  - https://github.com/settings/developers
    - Callback URL: http://localhost:2746/oauth2/callback
- Add `127.0.0.1 dex.argo.svc.cluster.local` to `/etc/hosts`

```sh
# Start server
cp -p .envrc.sample .envrc
direnv allow

minikube start --kubernetes-version="v1.23.0"

cd manifests/
kubectl apply -f resource.yaml

kubectl create secret -n argo generic argo-sso-creds \
    --from-literal=github-client-id=$GITHUB_CLIENT_ID \
    --from-literal=github-client-secret=$GITHUB_CLIENT_SECRET

kubectl create secret -n app generic minio-creds \
    --from-literal=minio-access-key=minio-access-key \
    --from-literal=minio-secret-key=minio-secret-key

helmfile apply

# Connect to server
kubectl port-forward -n argo svc/argo-workflows-server 2746:2746
kubectl port-forward -n argo svc/dex 5556:5556

open http://localhost:2746

# Run workflow
kubectl apply -f workflow.yaml
```

## Documents

- Helm chart parameters
  - https://artifacthub.io/packages/helm/argo/argo-workflows
  - https://artifacthub.io/packages/helm/dex/dex
  - https://artifacthub.io/packages/helm/minio/minio
- Guide
  - https://argoproj.github.io/argo-workflows/
  - https://dexidp.io/docs/connectors/github/

## ToDo

- [ ] Artifact repository
  - https://argoproj.github.io/argo-workflows/configure-artifact-repository/
- [ ] HA(with helm)
  - https://argoproj.github.io/argo-workflows/high-availability/
- [ ] Crash recovery
  - https://argoproj.github.io/argo-workflows/cron-workflows/#crash-recovery
- [ ] Slack notification
  - https://argoproj.github.io/argo-workflows/workflow-notifications/
- [ ] Cost optimization
  - https://argoproj.github.io/argo-workflows/cost-optimisation/
- [ ] Fargate(in AWS)
  - https://github.com/argoproj/argo-workflows/issues/2502
- [ ] Multiple containers
  - https://argoproj.github.io/argo-workflows/container-set-template/
