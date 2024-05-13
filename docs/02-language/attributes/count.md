# Count attribute

!!!note "A given resource cannot use both count and for_each."

By default, a resource configures a single object. (Similarly, a module/package resource includes a child module's contents into the configuration one time.) However, sometimes you want to manage several similar resource (like a fixed pool of interfaces) without writing a separate resource for each one. Kform has two ways to do this: count and for_each.

If a resource or module/package block includes a `count` argument whose value is a number, KForm will create that many instances.

## Usage

`count` is an attribute defined by the KForm language. You can use the `count` attribute in all block-types except the `input` block-type

The count attribute accepts a whole number, and creates that many instances of the resource or module/package. Each instance has a distinct resource associated with it, and each is separately created, updated, or deleted when the configuration is applied.

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: '["example", count.index].concat("-")'
  namespace: example-system
  annotations:
    kform.dev/count: input.context[0].data.count
data:
  image: count.index
```

## The count variable

In resources where the `count` attribute is specified, KForm automatically defines an additional `count` variable, enabling dynamic configuration adjustments for each instance. This `count` object facilitates modification of individual configurations within the resource. It comprises a single attribute:

- count.index: Represents the unique index number, starting from 0, associated with each instance, allowing precise identification and manipulation within the configuration."

## Using expressions in count

Numeric expressions can be used in the `count` attribute that are also used when Kform calculates the dependencies between resources.

## Referring to count resources.

A resource is always referred to as `<RESOURCE-TYPE>.<RESOURCE-ID>[INDEX]`. Normally Index is 0 but resources that use the `count` attribute could refer to an index different then 0.