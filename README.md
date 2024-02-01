
# rateme-nextgen-deployment

This repository is used to deploy the ratme-nextgen application, including the monitoring stack and Terraform scripts



## Helm

k8 deployment is done via helm-charts. All relevant files are located in `./helm` folder.

The `deployAll` script will install/upgrade the helm charts. You can configure the script(toggle between install/upgrade) or you can add additional release names. In default mode the ui, backend and database charts will be deployed in pipeline. Ressources that dont change often( ingress etc.) can be deployed manually if necessary. Pass the environemnt/namespace as argument to that script. If no argument is passed, default namespace will be used. frontend, backend and ui charts are encapsulated with the parent chart. 

```sh
helm install parent ./parent .  # will deploy all charts
```


```sh
  ./deployAll.sh [dev | prod]
```

## Deployment 

### Checklist 🛠️

Before deploying, ensure everything is ready.

- Provision Infrastructure (Run respective Terraform workflows)

- Dev and prod namespaces are available

- Check availability of grafana namespace


- Setup Secrets: 🔒
  => secret with name `db-security` and key `password` should be available for every namespace.
  => secret with name `springboot-security` and key `jwt-token` should be available for every namespace.

- Manually deploy changes related to ingress and secrets with kubectl 

- If IP of ingress changes, ensure dns records are up to date

Taking care of these steps will help ensure a successful deployment!

## Observability

Monitoring Dashboards are deployed on [monitoring.rateme-nextgen.com](http://monitoring.rateme-nextgen.com)

The services and deployments are installed via helm. Dashboards are exposed via ingress.

We use a preconfigured `kube-prometheus` stack to monitor the cluster

1. Install repo
```sh
  helm repo add prometheus-community https://prometheus-community.github.io/helm-charts
  helm repo update
```

2. Install charts:
```sh

helm install [RELEASE_NAME] prometheus-community/kube-prometheus-stack
```

3. Login to grafana

```sh
kubectl get secret monitoring-release-grafana -n default -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
```

At the moment dashboards are not persisted => reinstall after pod crashes.

### Monitoring deploy script

Deploy in `./monitoring` folder
```sh
  ./deploy.sh
```

### IaC / Terraform Workflows
We use terraform to provision our cloud ressources(e.g. gke cluster and node pool).

> `./terraform folder`

- `Terraform create` to provision cluster and nodes
- `Terraform destroy` to delete cluster and nodes

