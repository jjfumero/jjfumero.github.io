---
title: 'Configuring Unsloth on Linux for LLM Fine Tuning'
date: 2025-04-17
permalink: /posts/2025/04/17/unsloth-linux-install
author_profile: false
tags:
 - unsloth
 - LLM
 - finetuning
excerpt: "This guide details the configuration of Unsloth to build fine-tuned LLM models on NVIDIA GPUs on Linux systems. "
---

## What is `unsloth`?

[Unsloth](https://unsloth.ai/) is a Python framework focused on optimizing the fine-tuning of Large Language Models (LLMs) specifically for NVIDIA GPUs on both Linux and Windows. It leverages existing LLM frameworks for training and fine-tuning, such as the Hugging Face 🤗 Transformers library.

It's important to understand that `unsloth` is not a complete fine-tuning framework itself. Instead, it acts as an optimization layer, providing low-level utilities for quantization and performance enhancements to accelerate the fine-tuning process.

Despite the comprehensive documentation available on the [Unsloth website](https://docs.unsloth.ai/get-started/beginner-start-here), the installation steps weren't entirely straightforward for me. To help others facing the same, this guide details the configuration of Unsloth with an NVIDIA GPU on Fedora 41/42 and Ubuntu WSL systems.


## Installing Unsloth Locally

At the time of writing this post, `unsloth` requires `Python >= 3.9` and `<= 3.13`. Systems such as Fedora 41/42 and Ubuntu 24 come with a newer version of Python, so we need to set up an older version. 

### Installing `spack`

Hopefully, this is an easy process with the help of [`spack`](https://spack.io/), a manager software tool for Linux. 

```bash
git clone -c feature.manyFiles=true --depth=2 https://github.com/spack/spack.git

. spack/share/spack/setup-env.sh
```

### Installing Python 3.12.X

Then, we can install Python 3.12.7. Check all versions available with `spack`: [https://packages.spack.io/package.html?name=python](https://packages.spack.io/package.html?name=python).

```bash
spack install python@3.12.7
```


### Configure a new environment for Python

```bash
spack load python@3.12.7

python -m venv ~/bin/venv/
```

### Installing PyTorch

Next, we can install PyTorch. Note that, at the time of writing this post (April 2025), `unsloth` supports PyTorch 2.5.0 and 2.4.0.

```bash
pip install torch==2.5.0 torchvision==0.20.0 torchaudio==2.5.0
```


### Installing `unsloth`

Finally, we can install `unsloth`:


```bash
pip3 install "unsloth[cu124-torch250] @ git+https://github.com/unslothai/unsloth.git"
```

We also need to install a few libraries to store llama-based models:


### Some extra packages


We also need to install a few libraries to store llama-based models:

Fedora 41/42:

```bash
sudo dnf install ccache curl-devel
```

Ubuntu 24 WSL: 

```bash
sudo apt-get install cmake ccache libcurl4-gnutls-dev 
```


## How to update `unsloth`?

```bash
pip install --upgrade unsloth unsloth_zo
```

Now we have all tools available to create new fine-tuned models in our machines. 


## What's next?

You can start develop your own Python programs to build new LLM fine-tuned models based on LLama, Mistral, etc. The `unsloth` documentation covers a wide range of use-cases:

[https://docs.unsloth.ai/get-started/all-our-models](https://docs.unsloth.ai/get-started/all-our-models)

