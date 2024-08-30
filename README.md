# [31064](https://github.com/renovatebot/renovate/discussions/31064)

## Current behavior

Renovates correctly updates the `ref` in the URL for remote targets in kustomize, but it doesn't support multiple query parameters in the URL. If there are query parameters before `ref`, renovate doesn't work. If the query parameters are after `ref`, renovate deletes them in the update. Also, `version` is not treated as `ref`.

Working example from the file in [working](./working/kustomization.yaml):

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- https://github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.0.0
```

### Problem 1 - Renovate doesn't support query parameters before `ref` in the URL for remote targets in kustomize.

The following, from the file in [timeout](./timeout/kustomization.yaml) is not recognized by Renovate because there query parameters before `ref` in the URL:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- https://github.com/kubernetes-sigs/gateway-api/config/crd?timeout=120&ref=v1.0.0
```

### Problem 2 - Renovate deletes query parameters in the URL after `ref` for remote targets in kustomize.

The following, from the file in [ref-before-timeout](./ref-before-timeout/kustomization.yaml) is not updated correctly by Renovate because the query parameters are deleted:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- https://github.com/kubernetes-sigs/gateway-api/config/crd?ref=v1.0.0&timeout=120
```

### Problem 3 - Renovate doesn't treat `version` as `ref` in the URL for remote targets in kustomize.

The following, from the file in [version](./version/kustomization.yaml) is not recognized by Renovate because `version` is not treated as `ref`:

```yaml
apiVersion: kustomize.config.k8s.io/v1beta1
kind: Kustomization
resources:
- https://github.com/kubernetes-sigs/gateway-api/config/crd?version=v1.0.0
```


## Expected behavior

Renovate should be able to handle multiple query parameters in the URL for remote targets in kustomize, no matter the order of the query parameters. Renovate should also treat `version` as `ref` in the URL for remote targets in kustomize.


### Possible query parameters

[Remote targets in kustomize](https://github.com/kubernetes-sigs/kustomize/blob/master/examples/remoteBuild.md) support multiple query parameters:

 * `ref` - a [`git fetch`-able ref](https://git-scm.com/docs/git-fetch), typically a branch, tag, or full commit hash
   (short hashes are not supported)
 * `version` - same as `ref`. If `ref` is provided, this is ignored.
 * `timeout` (default `27s`) - a number in seconds, or a go duration. specifies
   the timeout for fetching the resource
 * `submodules` (default `true`) - a boolean specifying whether to clone
   submodules or not

## Link to the Renovate issue or Discussion

Reproduction repo for https://github.com/renovatebot/renovate/discussions/31064
