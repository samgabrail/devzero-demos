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


### Example with cache hits

ran this from user a:

```bash
devzero@big-fly-avht:~$ bazel clean --expunge
Starting local Bazel server and connecting to it...
INFO: Invocation ID: 4bdc1713-f4b8-4c33-aac3-c556a8218226
INFO: Starting clean (this may take a while). Consider using --async if the clean takes more than several minutes.
devzero@big-fly-avht:~$ bazel run :main
Starting local Bazel server and connecting to it...
INFO: Invocation ID: 46812c73-15d2-416f-a1ad-ae4d41e6fafb
INFO: Analyzed target //:main (100 packages loaded, 9340 targets configured).
INFO: Found 1 target...
Target //:main up-to-date:
  bazel-bin/main_/main
INFO: Elapsed time: 10.001s, Critical Path: 0.43s
INFO: 10 processes: 4 remote cache hit, 6 internal.
INFO: Build completed successfully, 10 total actions
INFO: Running command line: bazel-bin/main_/main
Hello, World!
```

and then ran this for user b:

```bash
devzero@ample-alligator-kyue:~$ bazel clean --expunge
INFO: Invocation ID: 2bf08c8c-d5b2-4f2c-ad53-1d52fef851b7
INFO: Starting clean (this may take a while). Consider using --async if the clean takes more than several minutes.
devzero@ample-alligator-kyue:~$ bazel run :main
Starting local Bazel server and connecting to it...
INFO: Invocation ID: 015d405a-7167-4c99-a42a-51710463026e
INFO: Analyzed target //:main (100 packages loaded, 9340 targets configured).
INFO: Found 1 target...
Target //:main up-to-date:
  bazel-bin/main_/main
INFO: Elapsed time: 9.220s, Critical Path: 0.36s
INFO: 10 processes: 4 remote cache hit, 6 internal.
INFO: Build completed successfully, 10 total actions
INFO: Running command line: bazel-bin/main_/main
Hello, World!
```

Explain the caching:

Let's break down what's happening with the caching between user a and user b:
User A's Build:

- Even though user A did a clean, they got some remote cache hits
  - 4 remote cache hit means 4 actions were found in the remote cache
  - 6 internal actions were local operations

User B's Build:

- User B sees similar performance because they're using the same remote cache
- They get the same 4 remote cache hit because those artifacts were already cached from user A's build
- The 6 internal actions are still needed locally

The key points:
- Both users share the same remote cache server
- Local clean --expunge only clears their local cache
- Remote cache persists between users
- Both users benefit from each other's builds because the artifacts are shared via remote cache

This is exactly how remote caching is supposed to work - when one user builds something, other users can reuse those build artifacts without having to rebuild them from scratch.