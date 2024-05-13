# Files and Directories

## File extension

Code in the `Kform` language is stored in plain `yaml` files using `.yaml` or `.yml`.

## Directories and modules/packages

A `KForm` module or package is a collection of `.yaml` or `.yml` files in a given directory. Nested directories are treated as independent `Kform modules/packages`.

`KForm` evaluates all of the configuration `yaml` files in a directory together, effectively treating the entire `KForm module/package` as a single document. Separating various blocks into different files is purely for the convenience of readers and maintainers, and has no effect on the `KForm module/package` behavior.

A `KForm module/package` can use module/package blockTypes to explicitly include other `KForm modules/packages` into the configuration. These child `KForm modules/packages` can come from local directories (nested in the parent module's directory, or anywhere else on disk), or from external sources like a Registry.

## Root KForm module/package

`KForm` always runs in the context of a single root module/package. A complete `KForm` configuration consists of a root module/package and the tree of child modules/packages (which includes the modules/packages called by the root module/package, any modules/packages called by those modules/packages, etc.). Sometimes child modules/packages are also called `mixins`.

In `Kform` CLI, the root module/package is the working directory where `Kform` is invoked/pointed at.