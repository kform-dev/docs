# Input KForm Resource

Input `KForm` resources let you customize aspects of a Kform module/package without altering the KForm module/package source code/configuration. This functionality allows you to share `KForm modules/packages` across different Kform configurations, making your `KForm module/package` composable and reusable.

When you declare an `input KRM resource` in the root module/package of your configuration, you can set their values using CLI options. When you declare `input KRM resource` in child modules/packages, the calling module/package should pass values in the module/package KRM resource.

Kform's input resources can be compared to function arguments in traditional programming.

## Input 

A KRM resource is defined as an `input` blockType by setting the annotation `kform.dev/block-type = input`. An Input resource is uniquely identified by the combination of the blockType and its resourceID. The resourceID is defined by the `kform.dev/resource-id` annotation or the `metadata.name` if the `kform.dev/resource-id` annotation is not present. A resourceID is mandatory and must be unique accross input resources in the same module/package. 

An input resource example is shown below

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
data:
  image: a.b
```

## Attributes

The following optional attributes can be defined for input resources through annotations:

- default - A default value which then makes the input KRM resource optional.
- description - This specifies the input KRM resource documentation.
- sensitive - Limits UI output when the variable is used in configuration.

### Default attribute

When an input resource is declared as default, the input KRM resource will be used if no input KRM resource with that resourceID is present when calling the module/package. A default input resource cannot have references/expressions with other resources.

### Description attribute

[description attribute](../attributes/description.md)

### Sensitive attribute

[sensitive attribute](../attributes/sensitive.md)

## Using Input Resources

Values from input resources can be accessed through expressions using the following syntax:
- input.<RESOURCE-ID>, where <RESOURCE-ID> matches the resourceID that is either defined by the `kform.dev/resource-id` annotation or the `metadata.name` if the `kform.dev/resource-id` annotation is not present.

In the example below, the input resource e.g. `input.context` can only be accessed in expressions within the module/package where it was declared. Also note that `KForm` stores the resources as a list, which means expressions must use `input.context[0]` to access the values of the first entry in the list.

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
data:
  image: a.b
```

When using input resources, they must be defined within the module/package, if not the `Kform` parser will throw an error

## Customizing input resources within a module/package

Input Resources that are declared in the root module/package of your configuration, can be customized through:

- cli options: -i (input file or input dir), which can source from a file or directory
- kform library using the inputData variable

The input resources, specified as dynamic input can omit the block-type annotation since kform knows these that dynamic input data are to be used as input block-types.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app
  namespace: my-system
  annotations:
    kform.dev/resource-id: context 
data:
  image: my-image
```

### command line

To customize the input resources used in a module/package, an input file or directory can be specified in the command line. 

```shell
kform apply -i <input file or input dir>
```

### kform library

When using Kform as a library, you can specify the input resources in the input data.

```go
kfrunner := runner.NewKformRunner(&runner.Config{
  PackageName:  cr.Spec.PackageID.Package,
  InputData:    inputData,
  ResourceData: resourceData,
  OutputData:   outputData,
})
```

### Declaring input resources

When using dynamic input resource, `KForm` will validate if the corresponding input is defined within the package, if not a WARNING message will be shown since this data will not be used.

e.g. if you define within your module/package the following input resource

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
data:
  image: a.b
```

The following dynamic input resource is specified with a resourceID that is unknown (e.g. ctx in this case), `KForm` will throw a warning to indicate the potential misconfiguration.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: my-app
  namespace: my-system
  annotations:
    kform.dev/resource-id: ctx
data:
  image: my-image
```

### Input Resource Preference

Kform will prioritize dynamic input resources over the once specified in the module/package itself.