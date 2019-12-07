---
layout: post
title: Build GameNetworkingSockets on Windows
---

# Build GameNetworkingSockets on Windows

## Build

### Install OpenSSL
* [https://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html)
* OpenSSL 1.1.1 or later
* Except "Light" suffix

### Install cmake
* https://cmake.org/download/

### Install ninja
* https://github.com/ninja-build/ninja/releases
* Copy ninja.exe to `C:\Ninja`
* Add `C:\Ninja` to Path 

### Enable vcvarsall
* Add `C:\Program Files (x86)\Microsoft Visual Studio 14.0\VC` to Path

### Build protobuf
* [https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)
* Download protobuf cpp 3.11
* Run VS2019 with Administrator
* Open command line and goto the folder
```cmd
mkdir cmake_build
cd cmake_build
vcvarsall amd64
cmake -G Ninja -Dprotobuf_BUILD_TESTS=OFF -Dprotobuf_BUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=c:\sdk\protobuf-amd64 ..\cmake
ninja
ninja install
```

### Build GameNetworkingSockets
* [https://github.com/ValveSoftware/GameNetworkingSockets/releases](https://github.com/ValveSoftware/GameNetworkingSockets/releases)
* Download GameNetworkingSockets 1.0
* Run VS2019 with Administrator
* Open command line and goto the folder
```shell
mkdir build
cd build
set PATH=%PATH%;C:\sdk\protobuf-amd64\bin
vcvarsall amd64
cmake -G Ninja ..
ninja
```

## Test example_chat

### Copy files
* GameNetworkingSocks dll files
* example_chat.exe
* protobuf dll files

### Run Server
```shell
example_chat server
```

### Run Clients
```shell
example_chat client 127.0.0.1
/nick PlayerAAA
hi
How are you?
/quit
```

```shell
example_chat client 127.0.0.1
/nick PlayerBBB
hello
Fine
/quit
```

Output would be following
```shell
example_chat client 127.0.0.1
/nick PlayerAAA
Ye shall henceforth be known as PlayerAAA
/nick PlayerBBB
Ye shall henceforth be known as PlayerBBB
PlayerAAA: hi
PlayerBBB: hello
PlayerAAA: How are you?
PlayerBBB: Fine
PlayerAAA departed
PlayerBBB departed
```
