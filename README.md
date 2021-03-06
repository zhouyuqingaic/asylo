# Asylo - An open and flexible framework for enclave applications

Copyright 2018 Asylo authors

## Documentation

The `v0.2` documentation can be found at [asylo.dev](https://asylo.dev/docs).

Example code can be found in the [asylo/examples](asylo/examples) directory.
This directory has a working Bazel workspace and example applications that can
be used as the start of your own project.

## Support

There are several ways of getting support with Asylo:

+   Join the [asylo-users](https://groups.google.com/forum/#!forum/asylo-users)
    mailing list to participate in discussions and get help troubleshooting
    problems.
+   Ask questions and find curated answers on
    [StackOverflow](https://stackoverflow.com) using the
    [asylo](https://stackoverflow.com/questions/tagged/asylo) tag.

## Build Environment in Docker (Recommended)

Asylo provides a custom Docker image that contains all required dependencies, as
well as Asylo's custom toolchain, which is required for compiling enclave
applications for various enclave backends.

```bash
docker run -it gcr.io/asylo-framework/asylo
```

See the [Dockerfile](asylo/distrib/Dockerfile) for an in-depth view of what's
inside the container image.

See this
[guide](https://cloud.google.com/container-registry/docs/pushing-and-pulling#pulling_images_from_the_registry)
for additional details on how to pull images from Google's Container Registry.

### Examples

#### Running the `hello_world` example

To run the `hello_world` example, first use the following set of commands to
grab the [examples source code](https://asylo.dev/asylo-examples.tar.gz), and
save it to any directory of your choice.

```bash
MY_PROJECT=~/asylo-examples
mkdir -p "${MY_PROJECT}"
wget -q -O - https://asylo.dev/asylo-examples.tar.gz | \
    tar -zxv --directory "${MY_PROJECT}"
```

Next, use Docker to build and run the `hello_world` application, which uses a
simulated enclave backend.

```bash
NAMES="${USER}"
docker run -it \
    -v bazel-cache:/root/.cache/bazel \
    -v "${MY_PROJECT}":/opt/my-project \
    -w /opt/my-project \
    gcr.io/asylo-framework/asylo \
    bazel run --config=enc-sim //hello_world -- --names="${NAMES}"
```

You can also set `NAMES` to a comma-separated list of names and see the
enclave's entry-point get invoked for each name.

##### Docker flags

In the above example, we use the following Docker flags:

+   `-v` is used to map local files to paths inside the container.
    -   The example project files are mapped to `/opt/my-project`.
    -   The local Bazel cache is mapped to `/root/.cache/bazel`, enabling
        incremental builds between `bazel` invocations.
+   `-w` is used to set the working directory in the container so that `bazel
    run` command is executed in the example project.

##### Bazel flags and workspace settings

In the above example, we use the following Bazel flags:

+   `--config=enc-sim` uses the Asylo toolchain to build the target for the
    enclave simulation backend.
+   `--names="${NAMES}"` is the argument passed to the `//hello_world` target.

Note: The example project from [examples/](asylo/examples) also picks up
additional Bazel configuration from the `tools/bazel.rc` file in that directory.
Remember to copy this file to a `tools/` sub-directory in future Bazel projects
you create so that you can make use of Asylo's toolchain configurations.

#### Running your own enclave application

You can follow the [steps above](#running-the-hello_world-example) to build your
own enclave application instead. You can use the examples code in `MY_PROJECT`
as the start of your own project, or simply change `MY_PROJECT` to point to your
own Bazel project instead.

#### Running the regression tests

To run our regression test suite, first clone the Asylo repository to a
directory of your choice.

```bash
ASYLO_SDK=~/asylo-sdk
git clone https://github.com/google/asylo.git "${ASYLO_SDK}"
```

The regression test suite includes tests that unit-test code directly as well as
tests that run inside a simulated enclave environment. You can run it with the
following command:

```bash
docker run -it \
    -v "${ASYLO_SDK}:/opt/asylo/sdk" \
    -v bazel-cache:/root/.cache/bazel \
    -w "/opt/asylo/sdk" \
    gcr.io/asylo-framework/asylo \
    asylo/test/run_enclave_tests.sh
```

See [Docker flags](#docker-flags) for a breakdown of the flags used in this
command. Note that in this command we also use `-v` to map the Asylo SDK source
files to `/opt/asylo/sdk`.

## Manual Installation

If you don't want to use the Asylo Docker image, you can manually install Asylo
and its dependencies instead.

See [INSTALL.md](INSTALL.md) for detailed installation steps.

### Examples

The following examples assume that the Asylo SDK was installed at `ASYLO_SDK`,
which can be a directory of your choice. For example:

#### Running the `hello_world` example

To run the `hello_world` example, first use the following commands to grab the
[examples source code](https://asylo.dev/asylo-examples.tar.gz) and save it to
any directory of your choice.

```bash
MY_PROJECT=~/asylo-examples
mkdir -p "${MY_PROJECT}"
wget -q -O - https://asylo.dev/asylo-examples.tar.gz | \
    tar -zxv --directory "${MY_PROJECT}"
```

Next, use Bazel to build and run the `hello_world` application, which uses a
simulated enclave backend:

```bash
cd "${MY_PROJECT}"
NAMES="${USER}"
bazel run --config=enc-sim //hello_world -- --names="${NAMES}"
```

Refer to [Bazel flags and workspace
settings](#bazel-flags-and-workspace-settings) for an explanation of the flags
and workspace configuration used in this example.

#### Running your own enclave application

You can follow the [steps above](#running-the-hello_world-example-1) to build
your own enclave application instead. You can use the examples code in
`MY_PROJECT` as the start of your own project, or simply change `MY_PROJECT` to
point to your own Bazel project instead.

#### Running the regression test suite

If you haven't already, use the following commands to clone the Asylo source
code repository and copy it to a directory of your choice.

```bash
ASYLO_SDK=~/asylo-sdk
git clone https://github.com/google/asylo.git "${ASYLO_SDK}"
```

The regression test suite includes tests that unit-test code directly as well as
tests that run inside a simulated enclave environment. You can run it with the
following command:

```bash
"${ASYLO_SDK}"/asylo/test/run_enclave_tests.sh
```

## Repository Structure & Status

This repository contains source code for the Asylo framework. The `v0.2` release
of the framework supports C++11 applications and a Bazel build environment.

The following packages contain source code that may be of particular interest to
users of the Asylo framework as well as those looking to contribute to Asylo
development.

+   [asylo](asylo)
    +   [crypto/](asylo/crypto)
        -   Crypto utilities and wrappers around BoringSSL.
    +   [distrib/](asylo/distrib)
        -   Asylo toolchain and dependencies.
    +   [examples/](asylo/examples)
        -   Sample applications written with the Asylo framework.
    +   [grpc/](asylo/grpc)
        +   [auth/](asylo/grpc/auth)
            -   gRPC authentication support.
        +   [util/](asylo/grpc/util)
            -   Utilities for using gRPC from a trusted application.
    +   [identity/](asylo/identity)
        -   Identity and attestation support.
    +   [platform/](asylo/platform)
        -   Implementation of enclave platforms and backends.
    +   [test/](asylo/test)
        -   Testing utilities provided to application writers.
    +   [util/](asylo/util)
        -   Common utilities provided for use both inside and outside an enclave
            environment.

## Disclaimer

This is not an officially supported Google product.
