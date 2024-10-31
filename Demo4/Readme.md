## Example

Create the following bazel workspace file structure for testing:

### main.cc

```cpp
#include <iostream>

int main( int argc, char *argv[] )
{
  std::cout << "Hello, World!" << std::endl;
}
```

### BUILD

```bazel
cc_binary(
    name = "main",
    srcs = ["main.cc"],
)
```

### WORKSPACE

Leave this file empty.

### Local build

Run `bazel run :main` to verify the build locally.

### Remote execution

```bash
bazel build --remote_executor=grpc://<your-devbox>:8980 :main
```

### Remote caching

```bash
bazel build --remote_cache=grpc://<your-devbox>:8980 :main
```
