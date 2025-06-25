---
title: 'How to disable auto-update in Fedora'
date: 2025-06-25
permalink: /posts/2025/06/25/fedora-disable-autoupdate
author_profile: false
tags:
 - Fedora
 - Linux
excerpt: "Linux command to disable Fedora's automatic updates at restart"
---


To disable the automatic updates at restart run the following command:

```bash
gsettings set org.gnome.software download-updates false
```

You can still update manually using `dnf`. This previous command disables the option.
This is important, especially if you have enabled/configured custom kernels, or third party modules (e.g., NVIDIA Drivers).

