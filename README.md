# Bazel build rules for GNU M4

## Overview

```python
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "rules_m4",
    sha256 = "b0309baacfd1b736ed82dc2bb27b0ec38455a31a3d5d20f8d05e831ebeef1a8e",
    urls = ["https://github.com/jmillikin/rules_m4/releases/download/v0.2.2/rules_m4-v0.2.2.tar.xz"],
)

load("@rules_m4//m4:m4.bzl", "m4_register_toolchains")

m4_register_toolchains(version = "1.4.18")
```

```python
load("@rules_m4//m4:m4.bzl", "m4")

m4(
    name = "hello_world",
    srcs = ["hello_world.in.txt"],
    output = "hello_world.txt",
)
```

```python
genrule(
    name = "hello_world_gen",
    srcs = ["hello_world.in.txt"],
    outs = ["hello_world_gen.txt"],
    cmd = "$(M4) $(SRCS) > $@",
    toolchains = ["@rules_m4//m4:current_m4_toolchain"],
)
```

### Bzlmod

Add the following line to `MODULE.bazel`:

```python
bazel_dep(name = "rules_m4", version = "0.2.2-bcr.0")
```

Optionally, additional `copts` may be configured for the toolchain build by
overriding the `m4_extension`. For instance, to disable warnings during the
toolchain build:

```python
bazel_dep(name = "rules_m4", version = "0.2.2-bcr.0")

m4 = use_extension("@rules_m4//m4:extensions.bzl", "m4_extension")

# Reconfigure the default toolchain.
m4.configure_toolchain(extra_copts = ["-w"])

# Reconfigure a non-default toolchain.
m4.configure_toolchain(version = "1.4.17", extra_copts = ["-w"])
```

## Toolchains

```python
load("@rules_m4//m4:m4.bzl", "M4_TOOLCHAIN_TYPE", "m4_toolchain")

def _my_rule(ctx):
    m4 = m4_toolchain(ctx)
    ctx.actions.run(
        tools = [m4.m4_tool],
        env = m4.m4_env,
        # ...
    )

my_rule = rule(
    _my_rule,
    toolchains = [M4_TOOLCHAIN_TYPE],
)
```
