# OpenSSL & cURL for Android

Prebuilt static OpenSSL and cURL libraries for Android, automatically compiled via GitHub Actions.

[![Auto Build](https://github.com/thedangcoder/curl_openssl_for_android/actions/workflows/auto_build.yml/badge.svg)](https://github.com/thedangcoder/curl_openssl_for_android/actions/workflows/auto_build.yml)

## Features

- Automatically checks for new OpenSSL/cURL releases daily
- Only builds when new versions are available (saves CI resources)
- Prebuilt static libraries with `-fPIC` flag
- Supports all modern Android ABIs

## Supported Platforms

| ABI | Min API | NDK |
|-----|---------|-----|
| `arm64-v8a` | 21 | r27c |
| `armeabi-v7a` | 21 | r27c |
| `x86_64` | 21 | r27c |
| `x86` | 21 | r27c |
| `riscv64` | 35 | r27c |

> **Note:** `armeabi`, `mips`, `mips64` are no longer supported (deprecated since NDK r17).

## Download

Get the latest prebuilt libraries from the [Releases](https://github.com/thedangcoder/curl_openssl_for_android/releases) page.

## Usage

### Directory Structure

The release ZIP contains:

```
Output/
├── curl/
│   ├── arm64-v8a/
│   │   ├── include/
│   │   └── lib/
│   ├── armeabi-v7a/
│   ├── riscv64/
│   ├── x86/
│   └── x86_64/
└── openssl/
    ├── arm64-v8a/
    │   ├── include/
    │   └── lib/
    ├── armeabi-v7a/
    ├── riscv64/
    ├── x86/
    └── x86_64/
```

### Integration with CMake

```cmake
set(LIBS_DIR ${CMAKE_CURRENT_SOURCE_DIR}/libs)

# OpenSSL
add_library(ssl STATIC IMPORTED)
set_target_properties(ssl PROPERTIES IMPORTED_LOCATION
    ${LIBS_DIR}/openssl/${ANDROID_ABI}/lib/libssl.a)

add_library(crypto STATIC IMPORTED)
set_target_properties(crypto PROPERTIES IMPORTED_LOCATION
    ${LIBS_DIR}/openssl/${ANDROID_ABI}/lib/libcrypto.a)

# cURL
add_library(curl STATIC IMPORTED)
set_target_properties(curl PROPERTIES IMPORTED_LOCATION
    ${LIBS_DIR}/curl/${ANDROID_ABI}/lib/libcurl.a)

# Include headers
include_directories(
    ${LIBS_DIR}/openssl/${ANDROID_ABI}/include
    ${LIBS_DIR}/curl/${ANDROID_ABI}/include
)

# Link to your target
target_link_libraries(your_target
    curl
    ssl
    crypto
)
```

### Integration with Android.mk

```makefile
# libcurl
include $(CLEAR_VARS)
LOCAL_MODULE            := libcurl
LOCAL_SRC_FILES         := libs/curl/$(TARGET_ARCH_ABI)/lib/libcurl.a
LOCAL_EXPORT_C_INCLUDES := libs/curl/$(TARGET_ARCH_ABI)/include
include $(PREBUILT_STATIC_LIBRARY)

# libssl
include $(CLEAR_VARS)
LOCAL_MODULE            := libssl
LOCAL_SRC_FILES         := libs/openssl/$(TARGET_ARCH_ABI)/lib/libssl.a
LOCAL_EXPORT_C_INCLUDES := libs/openssl/$(TARGET_ARCH_ABI)/include
include $(PREBUILT_STATIC_LIBRARY)

# libcrypto
include $(CLEAR_VARS)
LOCAL_MODULE            := libcrypto
LOCAL_SRC_FILES         := libs/openssl/$(TARGET_ARCH_ABI)/lib/libcrypto.a
LOCAL_EXPORT_C_INCLUDES := libs/openssl/$(TARGET_ARCH_ABI)/include
include $(PREBUILT_STATIC_LIBRARY)
```

Then link:

```makefile
LOCAL_STATIC_LIBRARIES := libcrypto libssl libcurl
```

## Build Locally

```bash
# Build OpenSSL
./openssl_build_new.sh <API> <ABI> <OPENSSL_VERSION> <NDK_VERSION>

# Build cURL (requires OpenSSL built first)
./curl_build.sh <API> <ABI> <CURL_VERSION> <NDK_VERSION> <OPENSSL_VERSION>
```

Example:
```bash
./openssl_build_new.sh 21 arm64-v8a 3.6.0 r27c
./curl_build.sh 21 arm64-v8a 8.18.0 r27c 3.6.0
```

## License

[Apache 2.0](LICENSE)
