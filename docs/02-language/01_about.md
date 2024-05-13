# KForm configuration language introduction

The `KForm` configuration language serves as a fundamental extension of the Kubernetes resource model ([KRM](KRM)). By utilizing `KForm`, users can craft configuration files that instruct the system on which plugins to install, what data to retrieve, and which resources to manage. Through the expressive capabilities of the `KForm` language, users can establish dependencies between resources and execute manipulations on Kubernetes resources using expressions.

## The KForm configuration language

The primary function of the `KForm` configuration language is to define Kubernetes resource manifests that render the resources defined by the `Kform package/module`. Its additional language features are designed to enhance the flexibility and convenience of resource definition.

A `Kform` configuration is a set of [KRM](KRM) manifests that tells `Kform` how to manage a given collection of [Kform KRM][KRM] resources. A `Kform` configuration can consist of multiple files in a given directory. We also call the collection of [KRM](KRM) manifests a `Kform package` or a `Kform module`.

The syntax of the `KForm` language consists of a few basic elements:

```yaml
apiVersion: <...>
kind: <...>
metadata:
  name: <...>
  annotations:
    kform.dev/block-type: <kform blockType>
    kform.dev/resource-type: <kform resourceType> 
    kform.dev/resource-id: <kform resourceID> 
    kform.dev/<kform attributes>: <kform attribute value or expression> 
spec:
  value: <expresion>
```

- A [KForm KRM][KRM] resource, also known as a 'block' in Kform, serves as the fundamental unit of a resource definition. These resources possess a `block type` comprising mandatory and/or optional attributes. Depending on the `block type`, a Kform `resource type` may be required.
- A `KForm` `resource ID` defines the identifier of a `Kform` block and is identified either by the explicit `kform.dev/resource-id` annotation or will be derived from the `metadata.name` if the `kform.dev/resource-id` annotation is not defined.
- Expressions in `KForm` represent values, either literally or by referencing and combining other values. They can appear as values for attributes or within other expressions.

The `KForm` configuration language operates in a declarative manner, describing the intended goal rather than the steps to achieve it. The ordering of KRM resources and the files where they are defined is irrelevant. Kform only considers files in the root Kform directory, allowing users to define KRM resources across multiple or individual files as desired. KForm establishes the order of operations based on implicit and explicit relationships between resources.

## Example

The following example `KForm module/package` describes a simple application deployment, to give a sense of the overall structure and syntax of the `KForm` configuration language. Similar configurations can be created for any KRM resource.

```yaml
---
# blockType: input, resourceID: context
# defines the input of the Kform package as a configmap KRM resource
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-app
  namespace: example-system
  annotations:
    kform.dev/block-type: input
    kform.dev/resource-id: context 
    kform.dev/default: "true"
data:
  image: a.b

---
# blockType: input, resourceID: kubernetes
# defines the kubernetes provider configuration
# when nothing is specified the default kubeconfig is used
apiVersion: kubernetes.provider.kform.io/v1alpha1
kind: ProviderConfig
metadata:
  name: kubernetes
  annotations:
    kform.dev/block-type: provider
spec: 

---
# blockType: resource, resourceID: ns, resourceType: kubernetes_manifest
# namespace KRM resource that will be applied to the system
# metadata.name is transformed using an expression
apiVersion: v1
kind: Namespace
metadata:
  name: input.context[0].metadata.namespace
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: ns 

---
# blockType: resource, resourceID: ns, resourceType: kubernetes_manifest
# namespace KRM resource that will be applied to the system
# expressions are used to transform the KRM manifest before applying to the system
kind: ServiceAccount
apiVersion: v1
metadata:
  name: input.context[0].metadata.name
  namespace: input.context[0].metadata.namespace
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: sa

---
# blockType: resource, resourceID: deployment, resourceType: kubernetes_manifest
# deployment KRM resource that will be applied to the system
# expressions are used to transform the KRM manifest before applying to the system
apiVersion: apps/v1
kind: Deployment
metadata:
  name: input.context[0].metadata.name
  namespace: input.context[0].metadata.namespace
  labels:
    app: "input.context[0].metadata.name"
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: deployment
spec:
  replicas: 1
  selector:
    matchLabels:
      app: "input.context[0].metadata.name"
  template:
    metadata:
      labels:
        app: "input.context[0].metadata.name"
    spec:
      serviceAccountName: "input.context[0].metadata.name"
      containers:
      - name: "input.context[0].metadata.name"
        image: "input.context[0].data.image"
        imagePullPolicy: Always
```

[KRM]: https://github.com/kubernetes/design-proposals-archive/blob/main/architecture/resource-management.md