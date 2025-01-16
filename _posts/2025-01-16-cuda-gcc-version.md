---
title: 'How to Fix CUDA GCC Unsuported Versions on Linux'
date: 2025-01-16
permalink: /posts/2025/01/16/cuda-gcc-versions
author_profile: false
tags:
 - CUDA
 - GCC
 - Versions
 - Linux
excerpt: "How to Fix CUDA GCC Unsuported Versions on Linux"
---


When compiling CUDA programs on Linux, you might encounter compatibility issues between the NVIDIA CUDA Compiler (nvcc) and newer versions GCC.
This conflict arises because `nvcc` has specific GCC version requirements, and using an unsupported GCC version can lead to compilation errors, as the one described as follows:


```bash
$ nvcc -ccbin g++ -m64 -gencode arch=compute_50,code=sm_50 -gencode arch=compute_52,code=sm_52 -gencode arch=compute_60,code=sm_60 -gencode arch=compute_61,code=sm_61 -gencode arch=compute_70,code=sm_70 -gencode arch=compute_75,code=sm_75 -gencode arch=compute_80,code=sm_80 -gencode arch=compute_86,code=sm_86 -gencode arch=compute_89,code=sm_89 -gencode arch=compute_90,code=sm_90 -gencode arch=compute_90,code=compute_90 sample.cu -o sample
In file included from /usr/local/cuda/include/cuda_runtime.h:82,
                 from <command-line>:
/usr/local/cuda/include/crt/host_config.h:143:2: error: #error -- unsupported GNU version! gcc versions later than 13 are not supported! The nvcc flag '-allow-unsupported-compiler' can be used to override this version check; however, using an unsupported host compiler may cause compilation failure or incorrect run time execution. Use at your own risk.
  143 | #error -- unsupported GNU version! gcc versions later than 13 are not supported! The nvcc flag '-allow-unsupported-compiler' can be used to override this version check; however, using an unsupported host compiler may cause compilation failure or incorrect run time execution. Use at your own risk.
      |  ^~~~~
make: *** [Makefile:2: all] Error 1
```

To solve this, we need to use an older version of GCC (usually it is an older version, but if our Linux systems comes with a very old GCC, we will need to upgrade it).
A good and easy way to obtain this is via [spack](https://github.com/spack/spack).

## Setting up `spack`:

```bash
$ mkdir -p ~/bin
$ cd ~/bin/
$ git clone -c feature.manyFiles=true --depth=2 https://github.com/spack/spack.git
$ cd spack/bin
```

I also setup the environment in the `~/.bashrc` file.

```bash
## Spack
source $HOME/bin/spack/share/spack/setup-env.sh
```

### Install GCC 12.3.0 

```bash
$ spack install gcc@12.3.0
```

After the installation, we need to load it:

```bash
$ spack load gcc@12.3.0
$ gcc --version
gcc (Spack GCC) 12.3.0
Copyright (C) 2022 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

Now we can compile our CUDA programs. 
