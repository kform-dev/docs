# Description attribute

A description attribute can be used to describe the purpose of the resource.

## Usage

Below is an example usage of the description attribute.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: example-app
  namespace: example-system
  annotations:
    kform.dev/block-type: input
    kform.dev/resource-id: context 
    kform.dev/default: "true"
    kform.dev/description: "the context of the package, namespace, name, image"
data:
  image: a.b
```