---
title: "Leveraging RISC-V Vectorization: Accelerating Java Programs with TornadoVM and OCK"
collection: publications
permalink: /publication/2025-03-24-RISCV-EU-Summit25
excerpt: ''
date: 24/03/2025
venue: 'RISCV EU Summit 2025'
paperurl: ''
citation: 'Juan Fumero Alfonso*, Athanasios Stratikopoulos, Colin Davidson, Harald van Dijk, Uwe Dolinsky, Michail Papadimitriou, Maria Xekalaki, Christos-Efthymios Kotselidis. Leveraging RISC-V Vectorization: Accelerating Java Programs with TornadoVM and OCK. RISCV EU Summit 2025.'
---

### Abstract

This paper presents an approach to accelerate Java applications on RISC-V processors equipped with vector extensions. 
Our approach utilizes a two-stage compilation chain composed of two open-source compilation frameworks. 
The first compilation is performed by TornadoVM, a Java Framework that includes a Just-In-Time (JIT) compiler and a 
runtime system that translate Java Bytecode into OpenCL and SPIR-V. 
The second compilation is operated by the oneAPI Construction Kit (OCK), a programming framework that translates OpenCL 
and SPIR-V code into an efficient binary augmented with vector instructions for RISC-V CPUs. 
We also present a preliminary performance evaluation using matrix multiplication. 
Results demonstrate a substantial performance improvement in the code generated when compared against functionally equivalent 
single-threaded and multi-threaded Java implementations, achieving speedups up to 33x and 4.6x respectively.


Pre-print: [PDF](https://pure.manchester.ac.uk/ws/portalfiles/portal/361946410/main.pdf)

Code: [Link](https://github.com/beehive-lab/TornadoVM)

Bibtex:

```bibtex
@conference{d6b38f538dd149aba432017c90c14751,
title = "Leveraging RISC-V Vectorization: Accelerating Java Programs with TornadoVM and OCK",
abstract = "This paper presents an approach to accelerate Java applications on RISC-V processors equipped with vector extensions. Our approach utilizes a two-stage compilation chain composed of two open-source compilation frameworks. The first compilation is performed by TornadoVM, a Java Framework that includes a Just-In-Time (JIT) compiler and a runtime system that translate Java Bytecode into OpenCL and SPIR-V. The second compilation is operated by the oneAPI Construction Kit (OCK), a programming framework that translates OpenCL and SPIR-V code into an efficient binary augmented with vector instructions for RISC-V CPUs. We also present a preliminary performance evaluation using matrix multiplication. Results demonstrate a substantial performance improvement in the code generated when compared against functionally equivalent single-threaded and multi-threaded Java implementations, achieving speedups up to 33x and 4.6x respectively.",
keywords = "Compiler, Performance, RISC-V, Java, OCK, Vectorization, JIT Compiler",
author = "{Fumero Alfonso}, Juan and Athanasios Stratikopoulos and Colin Davidson and {van Dijk}, Harald and Uwe Dolinsky and Michail Papadimitriou and Maria Xekalaki and Christos-Efthymios Kotselidis",
year = "2025",
month = may,
day = "12",
language = "English",
}
```
