#
# cpp_api/CMakeLists.txt
#
#
# The MIT License
#
# Copyright (c) 2018-2021 TileDB, Inc.
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.
#

# Add custom target 'examples_coo'
add_custom_target(examples_cpp DEPENDS tiledb_shared)

# Function that builds an executable per example
function(build_TileDB_example_cppapi TARGET)
  add_executable(${TARGET}_cpp EXCLUDE_FROM_ALL ${TARGET}.cc)
  target_link_libraries(${TARGET}_cpp PUBLIC local_install tiledb_shared)
  if (NOT WIN32)
    # On Linux, must explicitly link -lpthread -ldl in order for static linking
    # to libzstd or libcurl to work.
    target_link_libraries(${TARGET}_cpp PUBLIC pthread dl)
  endif()
  add_dependencies(examples_cpp ${TARGET}_cpp)
endfunction()

# Get the example sources
file(GLOB TILEDB_EXAMPLE_SOURCES_CPPAPI "*.cc")

# Iterate over all example sources and call the build function
foreach(EXAMPLE_SOURCE ${TILEDB_EXAMPLE_SOURCES_CPPAPI})
  # Get the binary name
  string(REGEX
    REPLACE "^${CMAKE_CURRENT_SOURCE_DIR}/" ""
    EXAMPLE_STRIPPED ${EXAMPLE_SOURCE}
  )
  string(REGEX
    REPLACE ".cc$" ""
    EXAMPLE_BIN ${EXAMPLE_STRIPPED}
  )

  # Build example executable
  build_TileDB_example_cppapi(${EXAMPLE_BIN})
endforeach()
