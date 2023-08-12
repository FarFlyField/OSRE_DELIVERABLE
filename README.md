# OSRE_DELIVERABLE
## Introduction
This repository includes the artifacts and instructions needed to reproduce the **GPU Emulator** proposed in [OSRE 2023](https://ucsc-ospo.github.io/project/osre23/utexas/gpuemulator/). The goal of the emulator is to reproduce the results of GPU system research without using actual GPUs in order to avoid competition for GPU resources. We do so by modifying the software to emulate the same behavior as if using a real GPU, for more details please check out the proposal [here](https://docs.google.com/document/d/1CcNbvbNAmY0XkV9ckjHnILdMh92h1wqLUYqpT6qIsZY/edit). 

I will also provide the python scripts needed to reproduce the graphs we have reproduced in paper [Analyzing and Mitigating Data Stalls in DNN Training](chrome-extension://efaidnbmnnnibpcajpcglclefindmkaj/https://vldb.org/pvldb/vol14/p771-mohan.pdf). 

The **GPU emulator** is embedded in the following softwares which include: 
* **Datastall**: this is where you will run the experiments to get data. It includes: 
  * **Main application** code to train images
  * **GPU Profile** information file, contains the time profiling data from GPUs. 
* Modified **PyTorch** souce code that contain emulator parts
* Modified **TorchVision** source code that contain emulator parts
* **mlock** module that helps assign memory

Follow my instructions and let me layout the steps first: 
1.  steps of installation on CPU, include cloning the needed repositories and set them up. 
2.  navigate into the directory to run the exepriments. 
3. learn how to use the scripts to reproduce the images. 

Now let me take you to the first step. 
## Step 1: Set up on CPU-only node
Because this is a GPU Emulator, our goal is to run experiments without using GPU, and here is the instruction about setting up the environment on CPU-only machine. 
#### Note: the commands below should be run on your reserved Chameleon node connected through ssh, not your local laptop.

1. Chameleon image

Please use image "ubuntu20-xxx."

"xxx" means we don't care about what's there, as long as it's using ubuntu20.

Successful tests:
- TACC "zen3" node, image "CC-Ubuntu20.04". Tested by Meng (May 28 2023)
- "xxx" node, image "xxx". Tested by xxx (time xxx)

2. Set up ssh
```
ssh-keygen -t rsa -b 4096
ssh-keyscan -t rsa github.com >> ~/.ssh/known_hosts
cat ~/.ssh/id_rsa.pub
```

Copy and paste into: https://github.com/settings/keys

3. clone this repo to local

DON'T COPY IT INTO THIS REPOSITORY! I have the needed applications in this repository but everything that runs here can also run in repo gpufs, so it is for the best case that you have gpufs as well. 

```
git clone git@github.com:mengwanguc/gpufs.git
cd gpufs/
git checkout Haoran_reproduce_graphs
```
Make sure you are on branch Haoran_reproduce_graphs so that you can get access to the scripts used to run the experiments. You will also find other useful things in this repo, but I will gather what's being needed in this repository so that you may only use gpufs as a backup. 

4. Install conda

```
wget https://repo.anaconda.com/archive/Anaconda3-2021.11-Linux-x86_64.sh
bash Anaconda3-2021.11-Linux-x86_64.sh
```

When it prompts to ask "yes|no", always put "yes".

After installation, log out the terminal (just close terminal) and log in again. You should see your terminal outputs:

```
(base) cc@ubuntu:~/
```
Bash again after logging in. 
```
bash Anaconda3-2021.11-Linux-x86_64.sh
```

5. Install packages required for builing pytorch

```
conda install -y astunparse numpy ninja pyyaml mkl mkl-include setuptools cmake cffi typing_extensions future six requests dataclasses
```


6. Download and build mlock (which can allocate page-locked memory)

```
cd ~
git clone git@github.com:gustrain/mlock.git
cd mlock
python setup.py install
```

7. Download and build minio

```
cd ~
git clone git@github.com:gustrain/minio.git
cd minio
python setup.py install
```



8. Download our custom pytorch and build it (Note that we use "export USE_CUDA=0" to not install any cuda/GPU-related things.)

```
cd ~
git clone git@github.com:mengwanguc/pytorch-meng.git
cd pytorch-meng
git submodule update --init --recursive

export CMAKE_PREFIX_PATH=${CONDA_PREFIX:-"$(dirname $(which conda))/../"}
export USE_CUDA=0
git checkout gus-emulator-minio
python setup.py install
```

9. Download our custom torchvision and build it

```
conda install -y aiofiles

cd ~
git clone git@github.com:mengwanguc/torchvision-meng.git
cd torchvision-meng/
git checkout gus-min-io
python setup.py install
```

10. Update `/etc/security/limits.conf`

```
sudo nano /etc/security/limits.conf
```

Add the following text to the end of the file:

```
*   soft    memlock     unlimited
*   hard    memlock     unlimited
```

11. Reboot the machine, which will take a while, and may require you to try to reopen/reconnect to your machine. 

```
sudo reboot
```
The steps above allows you to setup everything needed to run the experiments. Again, do not install any of the above inside this repository, install them at your local, or the outside directory of the current directory. 

## 2. Navigate into the Datastall and run the experiments
Please head into **datastall** directory and checkout the README in there. Once you have completed the instructions on that README and know how to checkout the reproduced data, come back to this README and I will show you how to use the scripts to reproduce the graphs. 

## 3. Reproduce the graphs
