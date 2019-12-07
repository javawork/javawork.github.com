---
layout: post
title: You're up and running!
---

Next you can update your site name, avatar and other options using the _config.yml file in the root of your repository (shown below).

![_config.yml]({{ site.baseurl }}/images/config.png)

The easiest way to make your first post is to edit this one. Go into /_posts/ and update the Hello World markdown file. For more instructions head over to the [Jekyll Now repository](https://github.com/barryclark/jekyll-now) on GitHub.

# Build GameNetworkingSockets on Windows

## Build

### Install OpenSSL
* [https://slproweb.com/products/Win32OpenSSL.html](https://slproweb.com/products/Win32OpenSSL.html)
* OpenSSL 1.1.1 or later
* Except "Light" suffix

### Build protobuf
* [https://github.com/protocolbuffers/protobuf/releases](https://github.com/protocolbuffers/protobuf/releases)
* Download protobuf cpp 3.11
* Run VS2019 with Administrator
* Open command line and goto the folder
```console
mkdir cmake_build
cd cmake_build
vcvarsall amd64
cmake -G Ninja -Dprotobuf_BUILD_TESTS=OFF -Dprotobuf_BUILD_SHARED_LIBS=ON -DCMAKE_INSTALL_PREFIX=c:\sdk\protobuf-amd64 ..\cmake
ninja
ninja install
```
