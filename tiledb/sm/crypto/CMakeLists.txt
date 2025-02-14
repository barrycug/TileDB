#
# tiledb/sm/crypto/CMakeLists.txt
#
# The MIT License
#
# Copyright (c) 2021 TileDB, Inc.
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

#
# `filter` object library
#
add_library(crypto OBJECT crypto.cc crypto_openssl.cc crypto_win32.cc encryption_key.cc)
target_link_libraries(crypto PUBLIC buffer $<TARGET_OBJECTS:buffer>)
if(WIN32)
    if(MSVC)
        find_library(BCRYPT_LIBRARY bcrypt)
        message(STATUS "Found Win32 lib bcrypt: ${BCRYPT_LIBRARY}")
        target_link_libraries(crypto PUBLIC ${BCRYPT_LIBRARY})
    else()
        message(STATUS "Linking to Win32 lib bcrypt")
        target_link_libraries(crypto PUBLIC -lbcrypt)
    endif()
else()
    find_package(OpenSSL_EP REQUIRED)
    target_link_libraries(crypto PUBLIC OpenSSL::Crypto)
endif()
# OpenSSL-3 deprecates MD5
if(MSVC)
    set_source_files_properties(crypto_openssl.cc PROPERTIES COMPILE_OPTIONS "/wd4996")
else()
    set_source_files_properties(crypto_openssl.cc PROPERTIES COMPILE_OPTIONS "-Wno-deprecated-declarations")
endif()
#
# Test-compile of object library ensures link-completeness
#
add_executable(compile_crypto EXCLUDE_FROM_ALL)
target_link_libraries(compile_crypto PRIVATE crypto)
target_sources(compile_crypto PRIVATE test/compile_crypto_main.cc)
