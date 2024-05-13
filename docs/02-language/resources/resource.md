# Resource KForm Resource

The `KForm resource` block defines that you want a particular managed object to exist with a specific configuration. When applying the Kform configuration, kform manages the lifecycle of the given managed object, by either creating, updating or deleting the managed object through a provider.

## Resource 

A KRM resource is defined as a `resource` blockType by setting the annotation `kform.dev/block-type = resource`. While the blockType is defined as resource, a specific `kform.dev/resource-type` has to be defined for a blockType = resource. On top to uniqly identify a resource a resourceID is defined through the `kform.dev/resource-id` annotation or the `metadata.name` if the `kform.dev/resource-id` annotation is not present. A resourceID is mandatory and must be unique accross input resources in the same module/package. 

A resource example is shown below, where the resource-type = kubernetes_manifest and resource-id = ns

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: ns 
```

## Provider

Resource are managed though a provider, which offers a collection of resource types. Providers are distributed independently from Kform, but Kform can install the providers when initializing a working module/package.

To manage resources, a Kform module/package must specify the required providers. Refer to Provider Requirements for additional information.

Most providers need some configuration to access their remote API, which is provided by the root module. Refer to Provider Configuration for additional information.

Based on a resource type's name, Kform can usually determine which provider to use. By convention, resource type names start with their provider's preferred local name. When using multiple configurations of a provider or non-preferred local provider names, you must use the provider attribute to manually choose a provider configuration.

## Attributes

The following optional attributes can be defined for resources through annotations:

- description - for specifying the resource documentation.
- sensitive - for limiting the output of the configuration.
- depends_on - for specifying hidden dependencies.
- count -  to create multiple resource instances according to a number or can serve as a conditional expression
- for_each - to create multiple resource instances according to a map, or set of strings
- provider - for selecting a specific provider configuration
- lifecycle - for determining if Kform should delete or orphan the managed resource
- precondition - todo
- postcondition - todo
- provisioner - todo

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

### Provider attribute

[provider attribute](../attributes/provider.md)

### Lifecycle attribute

[lifecycle attribute](../attributes/lifecycle.md)

## Deleting a resource

To remove a resource from Kform, simply delete the resource block from your Kform configuration.

By default, after you remove the resource block, Kform will plan to destroy any real object managed by that resource.

Sometimes you may wish to remove a resource from your Kform configuration without destroying the real object it manages. In this case, the resource will be removed from the Kform state, but the real object will not be destroyed. In order to do so add the lifecycle annotation = orphan as per example below

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: my-namespace
  annotations:
    kform.dev/block-type: resource
    kform.dev/resource-type: kubernetes_manifest 
    kform.dev/resource-id: ns 
    kform.dev/lifecycle: orphan 
```

## Custom condition checks

precondition ???
postcondition ???

## Operation Timout

to be specified in the provider config