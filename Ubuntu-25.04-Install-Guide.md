# Ubuntu 25.04 Install Guide (50 Series)
## Last Updated: 1 July 2025

This guide will walk you through the setup process for Ubuntu Desktop (25.04) to inference AI/ML models. This guide assumes you have a computer equip with a 50 series NVIDIA GPU and an empty ssd or m.2 drive available for installing linux. Note that install information changes often and that the commands documented in the code blocks might not be the most up to date and correct commands. It is always recommended to google the thing you are about to install first and double check the install instructions.

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

## NVIDIA cuDNN
Another very common library used by AI/ML applications is cuDNN. In order to install cuDNN you will need to visit https://developer.nvidia.com/cudnn-downloads which will also require you make an account. technically you do not need an account and can you the information below however this might not be the most up to date information. Here are the cudnn installation guided from nvida https://docs.nvidia.com/deeplearning/cudnn/installation/latest/index.html

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cudnn
```
You can follow instructions on how to validate that cuDNN is installed correctly but as long as I do not see any errors I usually skip this validation.

Note some AI/ML code requires older versions of cuDNN specifically - if this is the case the best course of action is to manually download older versions and make sure the appropriate files `*.so` etc are in the correct path. The code will complain about the specific missing files.

## Docker
Docker is an incediby powerful and useful tool that people often overlook. Installing Docker correctly system wide is a little tricky. Here is the current docker desktop setup for linux (https://docs.docker.com/desktop/setup/install/linux/ubuntu/)
```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl
sudo install -m 0755 -d /etc/apt/keyrings
sudo curl -fsSL https://download.docker.com/linux/ubuntu/gpg -o /etc/apt/keyrings/docker.asc
sudo chmod a+r /etc/apt/keyrings/docker.asc

# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.asc] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "${UBUNTU_CODENAME:-$VERSION_CODENAME}") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```
```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```
Verify the install with:
```bash
sudo docker run hello-world
```

There are a couple recommended post installtion actions for linux that will allow you to run docker without sudo:
https://docs.docker.com/engine/install/linux-postinstall/

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```
Verify the post installtion actions were sucessful by running the hellow world without `sudo`
```bash
docker run hello-world
```

## NVIDA Container Toolkit
You will need to follow some additonal steps to enable docker to use your NVIDIA GPU. Use the following commands to install and enable the container toolkit

```bash
curl -fsSL https://nvidia.github.io/libnvidia-container/gpgkey | sudo gpg --dearmor -o /usr/share/keyrings/nvidia-container-toolkit-keyring.gpg \
  && curl -s -L https://nvidia.github.io/libnvidia-container/stable/deb/nvidia-container-toolkit.list | \
    sed 's#deb https://#deb [signed-by=/usr/share/keyrings/nvidia-container-toolkit-keyring.gpg] https://#g' | \
    sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```bash
sudo apt-get update
```
```bash
export NVIDIA_CONTAINER_TOOLKIT_VERSION=1.17.8-1
  sudo apt-get install -y \
      nvidia-container-toolkit=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      nvidia-container-toolkit-base=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container-tools=${NVIDIA_CONTAINER_TOOLKIT_VERSION} \
      libnvidia-container1=${NVIDIA_CONTAINER_TOOLKIT_VERSION}
```
Next we will need to configure docker:
```bash
sudo nvidia-ctk runtime configure --runtime=docker
```
```bash
sudo systemctl restart docker
```
Then you will be able to verify that everything was installed correctly when you can run:
```bash
sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
```

## Miniconda (Python)
My recommended python environment is conda. conda will allow you to create different python environments super easily with different python version and also gives you some additional ways to install python libraries when the `pip install` does not work properly (becuase some python packages have aditional dependencies that do not ship with the pip library).

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

This will install miniconda. Next lets activate miniconda in all available shells:

```bash
source ~/miniconda3/bin/activate
```
```bash
conda init --all
```
Now every time you open a terminal you should see the base conda environment indicator `(base)`

```bash
(base) dream@dream-1-01:~$
```
Here is some additional information on using conda: https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html

## VSCODE
I recommend installing VSCODE as a code editor: https://code.visualstudio.com/download
Simply click the approapriate download link which should download a `.deb` file. find and right-click the .deb file then select open with app center. you should be presented with a window and an install button (this is a very common way to install linux applications).

## Tailscale Install
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
For remote connection through ssh and other computers you need to add the following permissions:
```bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
sudo ufw allow ssh
sudo ufw allow 22/tcp
```
