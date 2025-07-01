# Ubuntu 25.04 Install Guide (50 Series)
## Last Updated: 1 July 2025

This guide will walk you through the setup process for Ubuntu Desktop (25.04) to inference AI/ML models. This guide assumes you have a computer equip with a 50 series NVIDIA GPU and an empty ssd or m.2 drive available for installing linux.

## USB Bootloader
The first step in the process is to create an installation medium for Ubuntu Desktop (25.04).  Download the Ubuntu Desktop version 25.04 ISO from the following url: https://ubuntu.com/download/desktop . At the time of writing the 50 series NVIDIA drivers (driver version 570) are incompatible with the vanilla Ubuntu Desktop 24.04. It is possible to upgrade the ubuntu 24.04 kernel to support the 50 series drivers, but this process is very tedious and can often result in errors. When you install and setup Ubuntu Destkop 24.04 with an NVIDA 50 series GPU, the NVIDIA drivers will (most likley) not work out of the box and you will be unable to run common AI/ML programs/applications.

I have a Macbook and prefer to download the iso and flash (create a bootloader) using balena etcher https://etcher.balena.io/
For Windows users i believe you can use Rufus: https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview

Once you have flashed the usb drive (created a usb boot loader) you will need to plug the usb drive into your 50 series computer with empty drive and turn the computer on while rapidly pressing F2 + Delete + Backspace (depends on the motherboard you have). This will boot the coputer into BIOS where you will be able to select and boot directly into the installation media.

Here are some common search engine queries you can type in:
[TODO]

## Install Ubuntu Desktop 25.04
Once you have booted into your boot loader you will be presented with a menu for installing Ubuntu Desktop. Walk through the basic setup process. I prefer to select basic installation (only install the bare essentials and opt out of installing extra programs such a Libre Office etc.). It is also important to install thrid party drivers during this step and support for additional media types (these are very commonly used in AI/ML programs). Also you need to make sure that your machine is connected to the internet in order to be able to install third pary drivers and media. Do not select automatic download. When it is time to select where you will install the operating system I always select erase disk and install Ubuntu desktop. It is always my preference to use a dedicated drive for the Ubuntu install as opposed to installing Ubuntu along side windows or any other operating systems. On the topic of Windows, it is a good idea to install both Windows and Ubuntu in your system as this gives you access to all/every program available. Sometime programs work better on windows for instance or certain softwares like adobe or touch designer only work on windows. Linux however typically has higher performance and access to a greater open source programs. Most research projects are completed in linux and linux is where most of the cutting edge work is done (because Linux is very developer friendly). 

After setting up Ubuntu (you will need to reboot several times) you should see a desktop with various apps and menus. From here you can select show apps (bottom left) and select additonal drivers. By default the correct nvidia drivers should have been installed. You can verify that the nvidia-driver-570-open drivers are enabled in the additional drivers window. If the correct drivers are enabled you should be good to go.

The best way to verify that your system has the drivers installed correctly is to open the terminal and type the following command:

```bash
dream@dream-1-01:~$ nvidia-smi
Tue Jul  1 12:25:13 2025       
+-----------------------------------------------------------------------------------------+
| NVIDIA-SMI 570.133.07             Driver Version: 570.133.07     CUDA Version: 12.8     |
|-----------------------------------------+------------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id          Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |           Memory-Usage | GPU-Util  Compute M. |
|                                         |                        |               MIG M. |
|=========================================+========================+======================|
|   0  NVIDIA GeForce RTX 5090        Off |   00000000:01:00.0  On |                  N/A |
| 30%   33C    P8             20W /  575W |     272MiB /  32607MiB |      0%      Default |
|                                         |                        |                  N/A |
+-----------------------------------------+------------------------+----------------------+
                                                                                         
+-----------------------------------------------------------------------------------------+
| Processes:                                                                              |
|  GPU   GI   CI              PID   Type   Process name                        GPU Memory |
|        ID   ID                                                               Usage      |
|=========================================================================================|
|    0   N/A  N/A            9299      G   /usr/bin/gnome-shell                    226MiB |
|    0   N/A  N/A            9645      G   /usr/bin/Xwayland                         9MiB |
+-----------------------------------------------------------------------------------------+
```

You should see your driver version, and be able to see the Correct GPU is detected. in my case `GeForce RTX 5090`.

## Basic Ubuntu Packages
You will need these packages installed before we can install the items below.
```bash
sudo apt install curl
sudo apt install gcc
sudo apt install gedit
```

## NVIDIA Cuda Toolkit
You will need to install NVIDIA cuda toolkit. There are a variety of ways to install NVIDIA cuda toolkit, this is my prefered installation method. There is more detailed information /guide for installing cuda toolkit at https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html

GCC compilers are required to install cuda toolkit, it can easily be installed with `sudo apt install gcc`

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-9
```
There are additional post installation actions we must complete in order for our system to recognize the cuda toolkit system wide. We need to add the cuda toolkit location to our system path. We can install gedit (simple text editor) `sudo apt install gedit` to modify our system `.bashrc`.
```bash
gedit .bashrc
```
Once you have opened the `.bashrc` add these two lines:
```bash
export PATH=${PATH}:/usr/local/cuda-12.9/bin
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda-12.9/lib64
```
This tells our system that where we installed cuda toolkit. It is very common that AI/ML libraries will use the environment variables directly to interface with cuda. If these variables are not set your code will break.

You can verify that the install was sucesseful by opening a new terminal and typing the following:
```bash
dream@dream-1-01:~$ nvcc --version
nvcc: NVIDIA (R) Cuda compiler driver
Copyright (c) 2005-2025 NVIDIA Corporation
Built on Tue_May_27_02:21:03_PDT_2025
Cuda compilation tools, release 12.9, V12.9.86
Build cuda_12.9.r12.9/compiler.36037853_0
```




## Tailscale Install
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
