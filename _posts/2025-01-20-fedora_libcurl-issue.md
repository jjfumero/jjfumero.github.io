---
title: 'Fixing libcurl conflicts in Fedora 41'
date: 2025-01-20
permalink: /posts/2025/01/20/fedora-libcurl-issue
author_profile: false
tags:
 - Fedora
 - dnf
 - issue
excerpt: "Fixing libcurl conflicts in Fedora 41"
---


Recently I came across this error in Fedora 41, and I am not sure why the OS installed this library from different repos. 

```bash
$ sudo dnf update
Updating and loading repositories:
Repositories loaded.
Problem: installed package libcurl-minimal-8.9.1-3.fc41.x86_64 conflicts with libcurl(x86-64) provided by libcurl-8.9.1-3.fc41.x86_64 from updates
  - libcurl-8.9.1-3.fc41.i686 from updates has inferior architecture
  - cannot install the best update candidate for package libcurl-minimal-8.9.1-3.fc41.x86_64
  - cannot install the best update candidate for package libcurl-8.9.1-2.fc41.i686

Package                                          Arch         Version                                           Repository                     Size
Skipping packages with conflicts:
 libcurl                                         x86_64       8.9.1-3.fc41                                      updates                   809.3 KiB

Nothing to do.
```

Fortunately, there is a solution to this. Based on this [comment from the Fedora forums](https://discussion.fedoraproject.org/t/how-to-dnf-automatic-in-fedora-41/142733/5), we can swap the library to use. 


```bash
$ sudo dnf swap libcurl-minimal libcurl
Updating and loading repositories:
Repositories loaded.
Package "libcurl-8.9.1-2.fc41.i686" is already installed.

Package                                          Arch         Version                                           Repository                     Size
Removing:
 libcurl-minimal                                 x86_64       8.9.1-3.fc41                                      updates                   641.2 KiB
Downgrading:
 curl                                            x86_64       8.9.1-2.fc41                                      fedora                    796.2 KiB
   replacing curl                                x86_64       8.9.1-3.fc41                                      updates                   793.5 KiB
 libcurl-devel                                   x86_64       8.9.1-2.fc41                                      fedora                      1.3 MiB
   replacing libcurl-devel                       x86_64       8.9.1-3.fc41                                      updates                     1.3 MiB
Installing dependencies:
 libcurl                                         x86_64       8.9.1-2.fc41                                      fedora                    818.1 KiB

Transaction Summary:
 Installing:         1 package
 Replacing:          2 packages
 Removing:           1 package
 Downgrading:        2 packages

Total size of inbound packages is 2 MiB. Need to download 2 MiB.
After this operation, 180 KiB extra will be used (install 3 MiB, remove 3 MiB).
Is this ok [y/N]: y
[1/3] curl-0:8.9.1-2.fc41.x86_64                                                                           100% | 283.1 KiB/s | 315.1 KiB |  00m01s
[2/3] libcurl-0:8.9.1-2.fc41.x86_64                                                                        100% | 239.2 KiB/s | 361.9 KiB |  00m02s
[3/3] libcurl-devel-0:8.9.1-2.fc41.x86_64                                                                  100% | 547.2 KiB/s | 872.8 KiB |  00m02s
---------------------------------------------------------------------------------------------------------------------------------------------------
[3/3] Total                                                                                                100% | 820.5 KiB/s |   1.5 MiB |  00m02s
Running transaction
[1/8] Verify package files                                                                                 100% | 600.0   B/s |   3.0   B |  00m00s
[2/8] Prepare transaction                                                                                  100% |  23.0   B/s |   6.0   B |  00m00s
[3/8] Installing libcurl-0:8.9.1-2.fc41.x86_64                                                             100% |  57.1 MiB/s | 819.2 KiB |  00m00s
[4/8] Downgrading libcurl-devel-0:8.9.1-2.fc41.x86_64                                                      100% |   7.1 MiB/s |   1.4 MiB |  00m00s
[5/8] Downgrading curl-0:8.9.1-2.fc41.x86_64                                                               100% |  55.7 MiB/s | 798.6 KiB |  00m00s
[6/8] Removing libcurl-devel-0:8.9.1-3.fc41.x86_64                                                         100% | 126.8 KiB/s | 649.0   B |  00m00s
[7/8] Removing curl-0:8.9.1-3.fc41.x86_64                                                                  100% |   8.3 KiB/s |  17.0   B |  00m00s
[8/8] Removing libcurl-minimal-0:8.9.1-3.fc41.x86_64                                                       100% |  22.0   B/s |   7.0   B |  00m00s
Complete!
```


And then, you can update the system as usual:

```bash
$ sudo dnf update
Updating and loading repositories:
Repositories loaded.
Package                                          Arch         Version                                           Repository                     Size
Upgrading:
 curl                                            x86_64       8.9.1-3.fc41                                      updates                   793.5 KiB
   replacing curl                                x86_64       8.9.1-2.fc41                                      fedora                    796.2 KiB
 libcurl                                         i686         8.9.1-3.fc41                                      updates                   836.9 KiB
   replacing libcurl                             i686         8.9.1-2.fc41                                      fedora                    846.1 KiB
 libcurl                                         x86_64       8.9.1-3.fc41                                      updates                   809.3 KiB
   replacing libcurl                             x86_64       8.9.1-2.fc41                                      fedora                    818.1 KiB
 libcurl-devel                                   x86_64       8.9.1-3.fc41                                      updates                     1.3 MiB
   replacing libcurl-devel                       x86_64       8.9.1-2.fc41                                      fedora                      1.3 MiB

Transaction Summary:
 Upgrading:          4 packages
 Replacing:          4 packages
```


