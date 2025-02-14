#
# tiledb/api/c_api/filter/CMakeLists.txt
#
# The MIT License
#
# Copyright (c) 2022 TileDB, Inc.
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

include(common NO_POLICY_SCOPE)

list(APPEND SOURCES
    filter_api.cc
)
gather_sources(${SOURCES})

#
# Object library for other units to depend upon
#
add_library(capi_filter OBJECT ${SOURCES})
target_link_libraries(capi_filter PUBLIC export)
target_link_libraries(capi_filter PUBLIC all_filters $<TARGET_OBJECTS:all_filters>)
#
# Test-compile of object library ensures link-completeness
#
# Note: As of this time, this test-compile does not fully link because there's
# no `context` object library to link against. There's no point in testing
# link-completeness against the library as a whole.
#
add_executable(noncompile_capi_filter EXCLUDE_FROM_ALL)
target_link_libraries(noncompile_capi_filter PRIVATE capi_filter)
target_sources(noncompile_capi_filter PRIVATE test/compile_capi_filter_main.cc)

add_test_subdirectory()