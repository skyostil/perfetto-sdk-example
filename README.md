# Perfetto SDK example project

This repo contains an example project using the [Perfetto
SDK](https://perfetto.dev). It demonstrates how to instrument your application
with track events to give more context in developing, debugging and performance
analysis.

Dependencies:

- [CMake](https://cmake.org/)
- C++11

## Building

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

## Track event example

The [basic example](example.cc) shows how to instrument an app with track
events. Run it with:

```sh
$ ./example
```

The program will create a trace file in `example.pftrace`, which can be
directly opened in the [Perfetto UI](https://ui.perfetto.dev). The result
should look like this:

![Example trace loaded in the Perfetto UI](
  example.png "Example trace loaded in the Perfetto UI")

## System-wide example

While the above example only records events from the program itself, with
Perfetto it's also possible to combine app trace events with system-wide
profiling data (e.g., ftrace on Linux). The repository has a [second
example](example_system_wide.cc) which demonstrates this on Android.

Requirements:
- [Android NDK](https://developer.android.com/ndk)
- A device running Android Pie or newer

> Tip: It's also possible to sideload Perfetto on pre-Pie Android devices
> (TODO: Document this).

To build:

```sh
$ export NDK=/path/to/ndk
$ mkdir build_android
$ cd build_android
$ cmake -DCMAKE_TOOLCHAIN_FILE=$NDK/build/cmake/android.toolchain.cmake ..
$ make
```

Next, plug in an Android device into a USB port, download the example and run
it while simultaneously recording a trace using the `perfetto` command line
tool:

```sh
$ adb push example_system_wide ../system_wide_trace_cfg.pbtxt /data/local/tmp/
$ adb shell "\
    cd /data/local/tmp; \
    rm -f /data/misc/perfetto-traces/example_system_wide.pftrace; \
    cat system_wide_trace_cfg.pbtxt | \
        perfetto --config - --txt --background \
                 -o /data/misc/perfetto-traces/example_system_wide.pftrace; \
    ./example_system_wide"
```

Finally, retrieve the resulting trace:

```sh
$ adb pull /data/misc/perfetto-traces/example_system_wide.pftrace
```

When opened in the Perfetto UI, the trace now shows additional contextual
information such as CPU frequencies and kernel scheduler information.

![Example system wide-trace loaded in the Perfetto UI](
  example_system_wide.png "Example system-wide trace in the Perfetto UI")

> Tip: You can generate a new trace config with additional data sources using
> the [Perfetto UI](https://ui.perfetto.dev/#!/record) and replace
> `system_wide_trace_cfg.pbtxt` with the [generated config](
> https://ui.perfetto.dev/#!/record?p=instructions).

## Custom data source example

The [final example](example_custom_data_source.cc) shows how to use an
application defined data source to emit custom, strongly typed data into a
trace. Run it with:

```sh
$ ./example_custom_data_source
```

The program generates a trace file in `example_custom_data_source.pftrace`,
which we can examine using Perfetto's `trace_to_text` tool to show the trace
packet written by the custom data source:

```sh
$ trace_to_text text example_custom_data_source.pftrace
...
packet {
  trusted_uid: 0
  timestamp: 42
  trusted_packet_sequence_id: 2
  previous_packet_dropped: true
  for_testing {
    str: "Hello world!"
  }
}
...
```
