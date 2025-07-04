---
title: 'How to enable NVIDIA Nsight Compute CLI in Fedora'
date: 2025-07-24
permalink: /posts/2025/07/04/nvidia-ncu-enable-fedora
author_profile: false
tags:
 - Fedora
 - NVIDIA Nsight Compute CLI
 - Linux
excerpt: "How to enable NVIDIA Nsight Compute CLI in Fedora"
---


When working with CUDA, [NVIDIA's Nsight Compute CLI](https://docs.nvidia.com/nsight-compute/NsightComputeCli/index.html) (`ncu`) is an indispensable command-line tool for profiling your CUDA applications. It lets you peek under the hood to see exactly how your code is performing on the GPU.

For instance, you can easily profile a CUDA application with a command like this:


```bash
ncu -o myProfileData --set full ./cuda_sample
```

The command above generates a file `myProfileData.ncu-rep` which we can inspect with NVIDIA Nsight GUI to see all the profiled data. 

However, to set it up in Linux can be a bit tricky. 


## Setting It Up on Linux

The first time you use `ncu`, you might get the following error:

> The user does not have permission to access NVIDIA GPU Performance Counters on the target device 0. For instructions on enabling permissions and to get more information see https://developer.nvidia.com/ERR_NVGPUCTRPERM

Don't worry, this is a common setup issue and it's easy to fix! It means you need to grant users permission to access the GPU's performance counters.

What you need to do is to add a new line in the `/etc/modprobe.d/nvidia.conf` with the following content:

```bash
options nvidia NVreg_RestrictProfilingToAdminUsers=0
```

Then, you need to reboot the machine. Now you should be able to run the `ncu` command and profiler our CUDA programs. 

## A Note for Fedora and Similar Systems

Following the [NVIDIA guidelines](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters),
in some Linux distributions like Fedora, you might need to rebuild the `initrd`.
If the reboot alone doesn't do the trick, you'll need to rebuild it.

```bash
dracut --regenerate-all -f
```

### Links:

- [developer.nvidia](https://developer.nvidia.com/nvidia-development-tools-solutions-err_nvgpuctrperm-permission-issue-performance-counters)

- [https://hychiang.info/blog/2024/nsight-compute-permission-error](https://hychiang.info/blog/2024/nsight-compute-permission-error/)


