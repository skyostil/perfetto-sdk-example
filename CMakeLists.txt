cmake_minimum_required(VERSION 3.13)
project(perfetto-sdk-example)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(Threads)

# Define static library for Perfetto.
include_directories(perfetto/sdk)
add_library(perfetto STATIC perfetto/sdk/perfetto.cc)

# Link the library to the main executables.
add_executable(example example.cc example_tracing.cc)
add_executable(example_system_wide example_system_wide.cc example_tracing.cc)
add_executable(example_custom_data_source example_custom_data_source.cc)

target_link_libraries(example perfetto
                      ${CMAKE_THREAD_LIBS_INIT})
target_link_libraries(example_system_wide perfetto
                      ${CMAKE_THREAD_LIBS_INIT})
target_link_libraries(example_custom_data_source perfetto
                      ${CMAKE_THREAD_LIBS_INIT})

# On Android we also need the logging library.
if (ANDROID)
  target_link_libraries(example log)
  target_link_libraries(example_system_wide log)
  target_link_libraries(example_custom_data_source log)
endif (ANDROID)