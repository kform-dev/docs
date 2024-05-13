# Depends_on attribute

The `depends_on` attribute allows to handle hidden resource or module/package dependencies that `KFor`m cannot automatically infer. You only need to explicitly specify a dependency when a resource or module/package relies on another resource's behavior but does not access any of that resource's data in its arguments.

## Processing impact

The `depends_on` attributes instructs Kform to complete all actions on the dependency resource  before performing any actions on the resource declaring the dependency. When the dependency resource is a module/package, `depends_on` affects the order in which Kform processes all of the resources associated with that module.

Instead of `depends_on`, we recommend using expression references to imply dependencies when possible. Expression references let KForm understand which value the reference derives from and avoid planning changes if that particular value hasn’t changed, even if other parts of the upstream object have planned changes.

## Usage

You can use the `depends_on` attribute in all block-types except the `input` block-type. It requires a list of references to other resources or child modules/packages in the same calling module/package expressed as a comma, seperated string. This list cannot include arbitrary expressions because the `depends_on` value must be known before Kform knows resource relationships and thus before it can safely evaluate expressions.

It is good practice to document why a depend_on attribute is needed. The following example uses depends_on to handle a "hidden" dependency on the kubernetes_manifest.my-namespace.

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 

---
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
  namespace: my-namespace
  labels:
    app: my-app
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: deployment
    #depend_on hidden dependency to the kubernetes_manifest.my-namespace resource
    kform.dev/depends_on: kubernetes_manifest.my-namespace 
spec:
  replicas: 1
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      serviceAccountName: my-app
      containers:
      - name: my-app
        image: my-image
        imagePullPolicy: Always
```