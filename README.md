# Perfetto SDK example project

This repo contains an example project using the [Perfetto
SDK](https://perfetto.dev). It demonstrates how to instrument your application
with track events to give more context in developing, debugging and performance
analysis.

Dependencies:

- [CMake](https://cmake.org/)
- C++11

## Building and running

First, check out the repository (including submodules):

```sh
$ git clone --recurse-submodules https://github.com/skyostil/perfetto-sdk-example.git
```

Then, build using CMake:

```sh
$ cd perfetto-sdk-example
$ mkdir build
$ cd build
$ cmake ..
$ cmake --build .
```

Finally, you can run the example with:

```sh
$ ./example
```

It will create a trace file in `example.pftrace`, which can be directly
opened in the [Perfetto UI](https://ui.perfetto.dev). The result should look like this:

![Example trace loaded in the Perfetto UI](
  example.png "Example trace loaded in the Perfetto UI")