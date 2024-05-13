# For_each attribute

!!!note "A given resource cannot use both count and for_each."

By default, a resource configures a single object. (Similarly, a module/package resource includes a child module's contents into the configuration one time.) However, sometimes you want to manage several similar resource (like a fixed pool of interfaces) without writing a separate resource for each one. Kform has two ways to do this: count and for_each.

If a resource or module/package block includes a `for_each` argument whose value is a map or a list, KForm will iterate over these elements in the map or list.

## Usage

`for_each` is an attribute defined by the KForm language. You can use the `for_each` attribute in all block-types except the `input` block-type

The `for_each` attribute accepts a map or list, and creates that many instances of the resource or module/package. Each instance has a distinct resource associated with it, and each is separately created, updated, or deleted when the configuration is applied.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: '["example", each.index].concat("-")'
  namespace: example-system
  annotations:
    kform.dev/for_each: input.context[0].data.entries
data:
  image: each.key
```

## The for_each variable

In resources where the `for_each` attribute is specified, KForm automatically defines an additional `each` variable, enabling dynamic configuration adjustments for each instance. This `each` object facilitates modification of individual configurations within the resource. It comprises of two attribute:

- each.key: The map key (or list index) corresponding to this instance.
- each.value: The map or list value corresponding to this instance. 


## Using expressions in for_each

Map or list expressions can be used in the `for_each` attribute. These expressions are included in the dependency calculation such that the data of the expression is available when rendering the resource using the `for_each` expression.

## Referring to for_each resources.

A resource is always referred to as `<RESOURCE-TYPE>.<RESOURCE-ID>[INDEX]`. Normally Index is 0 but resources that use `for_each` could refer to an index different then 0.