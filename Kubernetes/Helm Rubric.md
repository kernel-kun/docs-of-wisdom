# Helm Rubric

## Command Syntax

### List

- List installed releases:  
  `helm list` *(add `-n <namespace>` for specific namespaces)*  
  Example: `helm list -n my-namespace`

- Show release history (revisions):  
  `helm history <release_name> [-n <namespace>]`

- Get post-install notes:  
  `helm get notes <release_name> [-n <namespace>]`

---

### Search for Charts

- Search within configured local repositories:  
  `helm search repo <chart_name>`

- Search Artifact Hub (remote aggregator):  
  `helm search hub <chart_name>`  
  *(Note: `helm search hub` uses [Artifact Hub](https://artifacthub.io), but requires web access and isn't configurable like `repo`)*

- View multiple versions of a chart (only for `search repo`):  
  `helm search repo <chart_name> --versions | grep <version>`  
  > ⚠️ `helm search hub` does **not** support the `--versions` flag.

---

### Repository Management

- Add a new chart repository:  
  `helm repo add <repo_name> <repo_url>`

- List added repositories:  
  `helm repo list`

- Remove a repository:  
  `helm repo remove <repo_name>`

- Update chart index across all repos:  
  `helm repo update`

---

### Install / Pull / Uninstall / Upgrade / Rollback Charts

- Install a chart:  
  `helm install <release_name> <repo/chart>`  
  or  
  `helm install <release_name> ./<chart_folder>`

  Examples:
  - Basic install:  
    `helm install my-nginx bitnami/nginx`
  - Dry run with debug info:  
    `helm install my-nginx bitnami/nginx --dry-run --debug`
  - Specify chart version and override values:  
    ```bash
    helm install my-nginx bitnami/nginx \
      --version "13.2.34" \
      --set key1=value1 --set key2=value2 \
      --values custom-values.yaml
    ```

- Pull chart files locally (without installing):  
  `helm pull <repo/chart> --untar`  
  Example: `helm pull bitnami/nginx --untar`

- Install from local chart directory:  
  `helm install <release_name> ./<chart_folder>`

- Uninstall a release:  
  `helm uninstall <release_name> [-n <namespace>]`

- Upgrade a release:  
  `helm upgrade <release_name> <repo/chart> [--version <ver>] [--values file.yaml]`

- Rollback to previous revision:  
  `helm rollback <release_name> <revision_number> [-n <namespace>]`  
  *(Use `helm history <release_name>` to view revisions)*

### Creating charts

- Bootstrap directory files : `helm create <our_chart_name>`
- 
