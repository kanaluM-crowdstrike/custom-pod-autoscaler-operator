# Usage

This will show the basic usage of the Custom Pod Autoscaler Operator, for more
indepth examples check out the
[Custom Pod Autoscaler repo](https://github.com/jthomperoo/custom-pod-autoscaler).

## Simple Custom Pod Autoscaler

```yaml
apiVersion: custompodautoscaler.com/v1
kind: CustomPodAutoscaler
metadata:
  name: python-custom-autoscaler
spec:
  template:
    spec:
      containers:
      - name: python-custom-autoscaler
        image: python-custom-autoscaler:latest
        imagePullPolicy: Always
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hello-kubernetes
  config:
    - name: interval
      value: "10000"
```

This is a simple Custom Pod Autoscaler, using an image called
`python-custom-autoscaler:latest`.

It provides the configuration option `interval` with a value of `10000` as an
environment variable injected into the container.

The target of this CPA is defined by `scaleTargetRef` - it targets a `Deployment`
called `hello-kubernetes`.

For more indepth examples check out the
[Custom Pod Autoscaler repo](https://github.com/jthomperoo/custom-pod-autoscaler).

## Using Custom Resources

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: python-custom-autoscaler
  annotations:
    myCustomAnnotation: test
---
apiVersion: custompodautoscaler.com/v1
kind: CustomPodAutoscaler
metadata:
  name: python-custom-autoscaler
spec:
  template:
    spec:
      containers:
      - name: python-custom-autoscaler
        image: python-custom-autoscaler:latest
        imagePullPolicy: Always
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hello-kubernetes
  provisionServiceAccount: false
  config:
    - name: interval
      value: "10000"
```

This is a Custom Pod Autoscaler that is similar to the basic one defined above, except
that it uses a custom `ServiceAccount`, with the annotation `myCustomAnnotation`.

Take note of the option inside the CPA `provisionServiceAccount: false`, which informs
the CPAO that the user will be providing their own `ServiceAccount`, so it should
not override it with its own provisioned `ServiceAccount`.

This custom resource provision is supported for all resources the CPAO manages:

- `provisionRole` - determines if a `Role` should be provisioned.
- `provisionRoleBinding` - determines if a `RoleBinding` should be provisioned.
- `provisionServiceAccount` - determines if a `ServiceAccount` should be
provisioned.
- `provisionPod` - determines if a `Pod` should be provisioned.

## Automatically Provisioning a Role with Access to the Kubernetes Metrics Server

> Note: this feature is only available in Custom Pod Autoscaler Operator `v1.1.0` and above

```yaml
apiVersion: custompodautoscaler.com/v1
kind: CustomPodAutoscaler
metadata:
  name: python-custom-autoscaler
spec:
  template:
    spec:
      containers:
      - name: python-custom-autoscaler
        image: python-custom-autoscaler:latest
        imagePullPolicy: Always
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hello-kubernetes
  roleRequiresMetricsServer: true
  config:
    - name: interval
      value: "10000"
```

This is a Custom Pod Autoscaler that is similar to the ones defined above, except it provisions a role with access to
the Kubernetes metrics server.

Take note of the option inside the CPA `roleRequiresMetricsServer: true` which informs the CPAO that the CPA requires
access to the metrics server, so the role that is provisioned should include these accesses.

## Automatically Provisioning a Role that Supports Argo Rollouts

> Note: this feature is currently unreleased.

```yaml
apiVersion: custompodautoscaler.com/v1
kind: CustomPodAutoscaler
metadata:
  name: python-custom-autoscaler
spec:
  template:
    spec:
      containers:
      - name: python-custom-autoscaler
        image: python-custom-autoscaler:latest
        imagePullPolicy: Always
  scaleTargetRef:
    apiVersion: apps/v1
    kind: Deployment
    name: hello-kubernetes
  roleRequiresArgoRollouts: true
  config:
    - name: interval
      value: "10000"
```

This is a Custom Pod Autoscaler that is similar to the ones defined above, except it provisions a role with access to
the ability to manage [Argo Rollouts](https://argoproj.github.io/argo-rollouts/).

Take not of the option inside the CPA `roleRequiresArgoRollouts: true` which informs the CPAO that the CPA requires
the ability to manage Argo Rollouts, so the role that is provisioned should include these accesses.
