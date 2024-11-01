## Example

Create the following bazel workspace file structure for testing:

### main.go

```go
package main

import "fmt"

func main() {
    fmt.Println("Hello, World!")
}
```

### BUILD

```bazel
load("@io_bazel_rules_go//go:def.bzl", "go_binary")

go_binary(
    name = "main",
    srcs = ["main.go"],
)
```

### WORKSPACE

```bazel
load("@bazel_tools//tools/build_defs/repo:http.bzl", "http_archive")

http_archive(
    name = "io_bazel_rules_go",
    sha256 = "51dc53293afe317d2696d4d6433a4c33feedb7748a9e352072e2ec3c0dafd2c6",
    urls = [
        "https://mirror.bazel.build/github.com/bazelbuild/rules_go/releases/download/v0.40.1/rules_go-v0.40.1.zip",
        "https://github.com/bazelbuild/rules_go/releases/download/v0.40.1/rules_go-v0.40.1.zip",
    ],
)

load("@io_bazel_rules_go//go:deps.bzl", "go_register_toolchains", "go_rules_dependencies")

go_rules_dependencies()
go_register_toolchains(version = "1.20.7")
```

### Local build

Run `bazel run :main` to verify the build locally.

### Remote execution

```bash
bazel build --remote_executor=grpc://<your-devbox>:8980 :main
```

Example:

```bash
bazel build --remote_executor=grpc://atomic-sparrow-jjoh:8980 :main
```

### Remote caching

```bash
bazel build --remote_cache=grpc://<your-devbox>:8980 :main
```

Example:

```bash
bazel build --remote_cache=grpc://atomic-sparrow-jjoh:8980 :main
```

To check hits and misses:

```bash
bazel build --remote_cache=grpc://atomic-sparrow-jjoh:8980 :main --build_event_text_file=build_events.txt
```

Then view the build events:

```bash
cat build_events.txt
```
