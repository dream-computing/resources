# Ubuntu 25.04 Install Guide (50 Series)
## Last Updated: 1 July 2025

This guide will walk you through the setup process for Ubuntu Desktop (25.04) to inference AI/ML models. This guide assumes you have a computer equipped with a 50 series NVIDIA GPU and an empty SSD or M.2 drive available for installing Linux. Note that installation information changes often and the commands documented in the code blocks might not be the most up-to-date. It is always recommended to search for the latest installation instructions before proceeding.

## Creating Ubuntu Installation Media

Download the Ubuntu Desktop version 25.04 ISO from: https://ubuntu.com/download/desktop

At the time of writing, the 50 series NVIDIA drivers (driver version 570) are incompatible with vanilla Ubuntu Desktop 24.04. While it is possible to upgrade the Ubuntu 24.04 kernel to support the 50 series drivers, this process is tedious and error-prone. When you install Ubuntu Desktop 24.04 with an NVIDIA 50 series GPU, the NVIDIA drivers will (most likely) not work out of the box, preventing you from running common AI/ML applications.

**For macOS:** I prefer to download the ISO and flash it using Balena Etcher: https://etcher.balena.io/

**For Windows:** I believe you can use Rufus: https://ubuntu.com/tutorials/create-a-usb-stick-on-windows#1-overview

Once you have created the USB bootloader, plug the USB drive into your 50 series computer with an empty drive and turn on the computer while rapidly pressing F2 + Delete + Backspace (depending on your motherboard). This will boot the computer into BIOS where you can select and boot directly into the installation media.

**Search terms:** `ubuntu bootable usb` `boot from usb` `ubuntu 25.04 download`

## Installing Ubuntu

Once you have booted into your bootloader, you will be presented with a menu for installing Ubuntu Desktop. Walk through the basic setup process.

### Installation Type
I prefer to select minimal installation (only install the bare essentials and opt out of installing extra programs such as LibreOffice). This choice saves disk space and reduces system clutter - you can always install additional software later as needed.

### Installation Settings
- Make sure your machine is connected to the internet during installation
- Install third-party drivers (crucial for NVIDIA GPU support)
- Install support for additional media types (required for processing various data formats in ML workflows)
- Do not select automatic install - opt for manual install

### Disk Configuration
When selecting where to install the operating system, I always select "Erase disk and install Ubuntu Desktop." I prefer using a dedicated drive for the Ubuntu install rather than installing Ubuntu alongside Windows or other operating systems. This approach eliminates potential boot loader conflicts and ensures maximum performance for your Linux installation.

### Dual Boot Considerations
Regarding Windows, it's a good idea to install both Windows and Ubuntu on your system as this gives you access to all available programs. Some programs work better on Windows, and certain software like Adobe Creative Suite or TouchDesigner only work on Windows. Linux, however, typically offers higher performance and access to more open-source programs. Most research projects are completed on Linux, where cutting-edge work is done due to its developer-friendly nature. The performance advantage comes from Linux's lower overhead and better resource management, which is particularly noticeable when training large models or running intensive computations.

### Post-Installation Driver Verification
After setting up Ubuntu (you will need to reboot several times), you should see a desktop with various apps and menus. Select "Show Applications" (bottom left) and choose "Additional Drivers." By default, the correct NVIDIA drivers should have been installed. Verify that the nvidia-driver-570-open drivers are enabled in the Additional Drivers window. The "open" drivers are NVIDIA's open-source kernel modules, which often provide better compatibility with newer kernels.

**Search terms:** `ubuntu install third party drivers` `ubuntu additional drivers nvidia` `ubuntu dual boot windows`

The best way to verify that your system has the drivers installed correctly is to open the terminal and type:

```bash
nvidia-smi
```

You should see your driver version and verify that the correct GPU is detected. In my case: `GeForce RTX 5090`. The output shows important information including the driver version, CUDA version compatibility, GPU temperature, power usage, and memory utilization. The P8 state indicates the GPU is in a low-power idle state, which is normal when not running computations.

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
```

## System Setup

### Basic Packages
Install these packages before proceeding:
```bash
sudo apt install curl
sudo apt install gcc
sudo apt install gedit
```

These packages serve as fundamental building blocks for the rest of our setup. Curl is essential for downloading files from the command line, gcc is the GNU Compiler Collection required for CUDA compilation, and gedit provides a simple graphical text editor for configuration files.

### NVIDIA CUDA Toolkit
You will need to install the NVIDIA CUDA Toolkit. There are various installation methods; this is my preferred approach. CUDA is NVIDIA's parallel computing platform and programming model that allows developers to use NVIDIA GPUs for general purpose processing. Almost all modern ML frameworks rely on CUDA for GPU acceleration. For more detailed information, see: https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html

GCC compilers are required to install CUDA Toolkit (install with `sudo apt install gcc` if needed).

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-9
```

Complete these post-installation actions to make CUDA Toolkit recognized system-wide. Add the CUDA Toolkit location to your system PATH by modifying `.bashrc`. The `.bashrc` file is executed every time you open a new terminal, making it the perfect place to set environment variables:

```bash
gedit ~/.bashrc
```

Add these two lines:
```bash
export PATH=${PATH}:/usr/local/cuda-12.9/bin
export LD_LIBRARY_PATH=${LD_LIBRARY_PATH}:/usr/local/cuda-12.9/lib64
```

This tells your system where CUDA Toolkit is installed. AI/ML libraries commonly use these environment variables to interface with CUDA. Without them, your code will fail. The PATH variable allows you to run CUDA commands from any directory, while LD_LIBRARY_PATH tells the system where to find CUDA's shared libraries at runtime.

**Search terms:** `cuda toolkit ubuntu install` `cuda add to path linux` `nvcc command not found`

Verify the installation by opening a new terminal and typing:
```bash
nvcc --version
```

The nvcc command is the NVIDIA CUDA Compiler, and seeing this output confirms that CUDA is properly installed and accessible from your command line.

### NVIDIA cuDNN
Another essential library for AI/ML applications is cuDNN. cuDNN (CUDA Deep Neural Network library) is a GPU-accelerated library of primitives for deep neural networks. It provides highly tuned implementations for standard routines such as forward and backward convolution, pooling, normalization, and activation layers. Most deep learning frameworks use cuDNN under the hood for optimal performance. To install cuDNN, visit https://developer.nvidia.com/cudnn-downloads (requires creating an account). The information below may not be the most current. See NVIDIA's installation guide: https://docs.nvidia.com/deeplearning/cudnn/installation/latest/index.html

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get -y install cudnn
```

You can validate the cuDNN installation following NVIDIA's instructions, but I usually skip this step if no errors occur.

**Search terms:** `cudnn install ubuntu` `nvidia cudnn download` `cudnn version check`

Note: Some AI/ML code requires specific older versions of cuDNN. In such cases, manually download the required version and ensure the appropriate files (`*.so` etc.) are in the correct path. The code will indicate which files are missing. This situation often arises when working with older research code or specific framework versions that were developed against earlier cuDNN releases.

## Docker

Docker is an incredibly powerful tool that's often overlooked. Docker allows you to package applications and their dependencies into containers, ensuring consistency across different environments. For ML workflows, this means you can share your exact working environment with collaborators, avoiding the dreaded "it works on my machine" problem. Installing Docker correctly system-wide requires attention to detail. Here's the current Docker Desktop setup for Linux: https://docs.docker.com/desktop/setup/install/linux/ubuntu/

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

Verify the installation:
```bash
sudo docker run hello-world
```

Complete these recommended post-installation actions to run Docker without sudo. This is important for convenience and for scripts that need to run Docker commands: https://docs.docker.com/engine/install/linux-postinstall/

```bash
sudo groupadd docker
sudo usermod -aG docker $USER
newgrp docker
```

These commands create a docker group and add your user to it, allowing you to run Docker commands without elevated privileges. The newgrp command activates the changes immediately for the current session.

**Search terms:** `docker install ubuntu` `docker without sudo` `docker hello world test`

Verify by running hello-world without `sudo`:
```bash
docker run hello-world
```

### NVIDIA Container Toolkit
Follow these additional steps to enable Docker to use your NVIDIA GPU. The NVIDIA Container Toolkit allows Docker containers to access NVIDIA GPUs, enabling you to run GPU-accelerated applications inside containers. This is particularly useful for ML workflows where different projects may require different CUDA versions or dependencies.

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

Configure Docker to use the NVIDIA runtime:
```bash
sudo nvidia-ctk runtime configure --runtime=docker
```
```bash
sudo systemctl restart docker
```

**Search terms:** `nvidia container toolkit` `docker gpu support` `nvidia docker install`

Verify everything works correctly:
```bash
sudo docker run --rm --runtime=nvidia --gpus all ubuntu nvidia-smi
```

This command runs nvidia-smi inside an Ubuntu container with GPU access. If successful, you should see the same GPU information as when running nvidia-smi on your host system, confirming that Docker can properly access your GPU.

## Development Environment

### Miniconda (Python)
My recommended Python environment manager is Conda. It allows you to create different Python environments easily with various Python versions and provides additional installation methods when `pip install` doesn't work properly (some Python packages have additional dependencies not included with pip). Conda is particularly valuable for ML work because it manages not just Python packages but also system libraries and dependencies, preventing conflicts between different projects.

```bash
mkdir -p ~/miniconda3
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh -O ~/miniconda3/miniconda.sh
bash ~/miniconda3/miniconda.sh -b -u -p ~/miniconda3
rm ~/miniconda3/miniconda.sh
```

Activate Miniconda in all shells:

```bash
source ~/miniconda3/bin/activate
```
```bash
conda init --all
```

The conda init command modifies your shell configuration files to automatically activate conda whenever you open a terminal. This ensures conda's environment management is always available.

**Search terms:** `miniconda install linux` `conda init bash` `conda create environment`

Now every terminal will show the base Conda environment indicator:

```bash
(base) dream@dream-1-01:~$
```

This (base) prefix indicates you're in conda's base environment. You'll typically want to create separate environments for different projects to avoid dependency conflicts.

For more information on using Conda: https://docs.conda.io/projects/conda/en/stable/user-guide/getting-started.html

### VS Code
I recommend installing VS Code as a code editor: https://code.visualstudio.com/download

Click the appropriate download link to get a `.deb` file. Right-click the .deb file and select "Open with App Center." You'll see an install button (this is a common way to install Linux applications). VS Code is particularly well-suited for ML development with excellent Python support, integrated terminal, Git integration, and extensions for Jupyter notebooks.

**Search terms:** `vscode install ubuntu` `vscode deb file` `vscode python extension`

### System Monitoring Tools
Install these tools for real-time system monitoring:

```bash
sudo apt install nvtop
sudo apt install htop
```

`nvtop` is like htop but specifically for NVIDIA GPUs, showing real-time GPU usage, memory, temperature, and running processes in a nice interface. Run it by simply typing `nvtop` in the terminal. `htop` provides an interactive process viewer for CPU and system memory, making it easy to identify resource-hungry processes. Both tools are invaluable when training models or debugging performance issues.

**Search terms:** `nvtop ubuntu install` `htop vs top` `gpu monitoring linux`

## Remote Access

### Tailscale
Tailscale creates a secure private network between your devices, making it easy to access your ML workstation remotely without complex firewall configuration or exposing ports to the internet.

```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```

### SSH Server
For remote connections from other computers, add these permissions:
```bash
sudo apt install openssh-server
sudo systemctl enable ssh
sudo systemctl start ssh
sudo ufw allow ssh
sudo ufw allow 22/tcp
```

**Search terms:** `tailscale linux install` `ssh server ubuntu` `remote access ubuntu`

These commands install the SSH server, ensure it starts automatically on boot, and configure the firewall to allow SSH connections. This setup allows you to remotely access your workstation for long-running training jobs or to use your GPU resources from another machine.
