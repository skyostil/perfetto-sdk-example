cmake_minimum_required(VERSION 3.13)
project(perfetto-sdk-example)

find_package(Threads)

# Define static library for Perfetto.
include_directories(perfetto/sdk)
add_library(perfetto STATIC perfetto/sdk/perfetto.cc)

# Link the library to the main executable.
add_executable(example example.cc example_tracing.cc)

target_link_libraries(example perfetto ${CMAKE_THREAD_LIBS_INIT})