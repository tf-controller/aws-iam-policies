# An AWS IAM Policy Example for TF controller

Before using it, you need a Secret name `aws-credentials` to hold your Access and Secret keys.

```yaml
apiVersion: v1
kind: Secret
metadata:
  name: aws-credentials
  namespace: flux-system
data:
  access_key: <redacted>
  secret_key: <redacted>
```

Here's a copy-n-paste snippet to bootstrap the Terraform object for a Flux-ready system.

```yaml
cat <<EOF | kubectl apply -f -
---
apiVersion: source.toolkit.fluxcd.io/v1beta1
kind: GitRepository
metadata:
  name: aws-iam-policies
  namespace: flux-system
spec:
  interval: 30s
  url: https://github.com/tf-controller/aws-iam-policies
  ref:
    branch: main
---
apiVersion: kustomize.toolkit.fluxcd.io/v1beta2
kind: Kustomization
metadata:
  name: aws-iam-policies-ks
  namespace: flux-system
spec:
  prune: true
  interval: 2m
  path: "./infra"
  sourceRef:
    kind: GitRepository
    name: aws-iam-policies
  timeout: 3m
  healthChecks:
  - apiVersion: infra.contrib.fluxcd.io/v1alpha1
    kind: Terraform
    name: iam-policy
    namespace: flux-system
EOF

```
