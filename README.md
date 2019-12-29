# helm3-chart-inflator

Kustomize plugin to download a chart from git into a temporary and build the helm3 template chart.

This is really useful if you chart is hosted on a git repository instead and you don't want to download the files manually. Teams can just use the modules to import and use the charts they want.

Additionally, this plugin can use a custom `values.yaml` to be used by helm. This enables the template to be kustomized by environment.

## Requirements:

   - `python3`
   - `sh`
   - `git`
   - `helm3`

## Kustomization.yaml vars:

  - `metadata.name`: Helm release name
  - `gitRepository`: Helm git repositoy to checkout
  - `gitRef`: Git reference of the helm repository
  - `valuesFile`: Path of the values.yaml to be used by helm. Defaults to the values.yaml inside of the git repository.

## Installing:

```
version=v0.0.1
dir=kustomize/plugin/helm3-chart-inflator/${version}/inflator

mkdir -pv "${dir}" && (
  cd "${dir}" &&
  curl -OL "https://raw.githubusercontent.com/tehmoon/helm3-chart-inflator/${version}/inflator" &&
  chmod a+x inflator
) 
```

## Example with consul

kustomization.yaml:

```
---
resources:
  - namespace.yaml
namespace: test
generators:
  - inflators.yaml
```

namespace.yaml:

```
---
apiVersion: v1
kind: Namespace
metadata:
  name: test
```

inflator.yaml

```
---
apiVersion: helm3-chart-inflator/v0.0.1
kind: inflator
gitRepository: https://github.com/hashicorp/consul-helm
gitRef: origin/master
valuesFiel: values.yaml
metadata:
  name: first-release
```

values.yaml

```
---
tests:
  enabled: false
```

Build and deploy:

```
export XDG_CONFIG_HOME=$(pwd)
kustomize build --enable_alpha_plugins . | kubectl apply -f -
```
