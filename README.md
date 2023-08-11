# OSRE_DELIVERABLE
## Introduction
This repository includes the artifacts and instructions needed to reproduce the **GPU Emulator** proposed in [OSRE 2023](https://ucsc-ospo.github.io/project/osre23/utexas/gpuemulator/). The goal of the emulator is to reproduce the results of GPU system research without using actual GPUs in order to avoid competition for GPU resources. We do so by modifying the software to emulate the same behavior as if using a real GPU, for more details please check out the proposal [here](https://docs.google.com/document/d/1CcNbvbNAmY0XkV9ckjHnILdMh92h1wqLUYqpT6qIsZY/edit). 

I will also provide the python scripts needed to reproduce the graphs we have reproduced in paper [Analyzing and Mitigating Data Stalls in DNN Training](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://vldb.org/pvldb/vol14/p771-mohan.pdf). 

The **GPU emulator** is embedded in the following softwares which include: 
* **Main application** code to train images
* Modified **PyTorch** souce code that contain emulator parts
* Modified **TorchVision** source code that contain emulator parts
* **mlock** module that helps assign memory
* GPU Profile information file

The codes above are in the repository and I will provide specific steps about how to run them. 


