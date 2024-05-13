# Output KForm Resource

Output Resources serve as a means to share resource data beyond the confines of your module or package, enabling other components to utilize this information for subsequent operations.

Similar to return values in programming languages, Output Resources provide several benefits:

- Child modules or packages can utilize outputs to expose specific resource attributes to a parent module, facilitating modular design and encapsulation.
- Consumers of the root module or package can employ output resources to display selected values in the CLI output post-execution of `kform apply`.
- Output resources from the root module or package can be leveraged for various post-processing tasks, such as utilizing GitOps tools to apply the resources to the cluster, among other operations."

## Output Resource

A KRM resource is defined as an `output` blockType if no `kform.dev/block-type` annotation is present or when you explicitly set the annotation `kform.dev/block-type = output`. The flexibility of omitting the `kform.dev/block-type` annotation ensures that any KRM module/package can be consumed as a `KForm module.package`.

An output resource is uniquely identified by the combination of the blockType and its resourceID. The resourceID is defined by the `kform.dev/resource-id` annotation or the `metadata.name` if the `kform.dev/resource-id` annotation is not present. A resourceID is mandatory and must be unique accross ouput resources in the same module/package. 

The following examples show how to define output resources, the first omits the blockType and resourceID, while the second one explicitly sets the block-type and resource-id.

Example where the output block-type is implied

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-system
```

Example where the output block-type is explicitly defined

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-system
  annotations:
    kform.dev/block-type: output
    kform.dev/resource-id: ns 
```

## Attributes

The following optional attributes can be defined for input resources through annotations:

- description - specifies the resource documentation.
- sensitive - limits the output in the configuration.
- depends_on -  defines hidden resource dependencies
- count - allows to multiply a resource or can be used as a conditional expression
- for_each -  allows to multiply a resource

### Description attribute

[description attribute](../attributes/description.md)

### Sensitive attribute

[sensitive attribute](../attributes/sensitive.md)

### Depends_on attribute

[depends_on attribute](../attributes/depends_on.md)

### Count attribute

[count attribute](../attributes/count.md)

### For_each attribute

[for_each attribute](../attributes/for_each.md)