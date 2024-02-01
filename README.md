
# rateme-nextgen-deployment

This repository is used to deploy the ratme-nextgen application, including the monitoring stack and Terraform scripts

### IaC / Terraform Workflows
We use terraform to provision our cloud ressources(e.g. gke cluster and node pool).

> `./terraform folder`

- `Terraform create` to provision cluster and nodes
- `Terraform destroy` to delete cluster and nodes

### Deployment Workflows

> `./helm folder`
We are deploying the changes via helm. The helm charts for the application are encapsulated with the `main` chart(parent chart).
The parent chart will be installed when running `./deployAll.sh` script.
Following charts will be part of the installation:
- `backend`
- `database`
- `ui`

We use the `helm-deployment` workflow, this workflow is templated and is called for each environment

Deployment of monitoring stack is a bit different:
> `./monitoring folder`

`deploy.sh` script will install the kube-prometheus stack in the cluster.

### Github environments

We use Github environments to store environment specific data in objects like variables/environment secrets
e.g. the name of the namespace or the db secret.


