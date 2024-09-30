# Prerequisites

## CPU architecture

kform runs on both AMD and ARM based CPU

## Operating system

We tested on WSL for windows and Linux and darwin OS.

## kform

kform is a command line tool for executing kform plans/packages.

kform is a single binary built for linux and Mac OS, distributed via [ghreleases][ghreleases]. It


/// tab | linux/Mac OS

To download & install the latest release the following automated [installation script][installscript] can be used.

```bash
bash -c "$(curl -sL https://github.com/kform-dev/kform/raw/main/install.sh)"
```

As a result, the latest `kform` version will be installed in the /usr/local/bin directory and the version information will be printed out.

To install a specific version of `kform`, provide the version with -v flag to the installation script:

```bash
bash -c "$(curl -sL https://github.com/kform-dev/kform/raw/main/install.sh)" -- -v 0.0.1
```

///

/// tab | Packages

Linux users running distributions with support for deb/rpm packages can install kform using pre-built packages:

```bash
bash -c "$(curl -sL https://github.com/kform-dev/kform/raw/main/install.sh)" -- --use-pkg
```

///



[ghreleases]: https://github.com/kform-dev/kform/releases
[installscript]: https://github.com/kform-dev/kform/raw/main/install.sh