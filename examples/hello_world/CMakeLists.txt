# Copyright 2019 Google LLC
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.

cmake_minimum_required(VERSION 3.11)
project(hello_world)
set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

include(FetchContent)
FetchContent_Declare(highway GIT_REPOSITORY https://github.com/google/highway.git GIT_TAG bfc36a6e633af94e63ac4b91c687bf0354cb24e0)
FetchContent_MakeAvailable(highway)
FetchContent_Declare(sentencepiece GIT_REPOSITORY https://github.com/google/sentencepiece GIT_TAG 9045b2f60fa2b323dfac0eaef8fc17565036f9f9)
FetchContent_MakeAvailable(sentencepiece)



# Allow for both local and remote building)
option(BUILD_MODE "'local' or 'remote' git fetch for builds")
if (NOT BUILD_MODE)
  set(BUILD_MODE "remote")
endif()
if (BUILD_MODE STREQUAL "local")
  # Relative path to gemma.cpp from examples/hello_world/build/
  FetchContent_Declare(gemma SOURCE_DIR ../../..)
else()
  FetchContent_Declare(gemma GIT_REPOSITORY https://github.com/google/gemma.cpp.git GIT_TAG 4a924f179448dc83e46a2af9520c61b4ef56174c)
endif()
FetchContent_MakeAvailable(gemma)

if(NOT CMAKE_BUILD_TYPE)
    set(CMAKE_BUILD_TYPE "Release")
endif()

add_executable(hello_world run.cc)
target_link_libraries(hello_world hwy hwy_contrib sentencepiece libgemma)
FetchContent_GetProperties(sentencepiece)
target_include_directories(hello_world PRIVATE ${sentencepiece_SOURCE_DIR})
target_compile_definitions(hello_world PRIVATE $<$<PLATFORM_ID:Windows>:_CRT_SECURE_NO_WARNINGS NOMINMAX>)
target_compile_options(hello_world PRIVATE $<$<PLATFORM_ID:Windows>:-Wno-deprecated-declarations>)
