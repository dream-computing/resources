# Install and Set Up Guide

Follow this guide to setup a system with GPU drivers, CUDA toolkit, NVIDIA cuDNN, Miniconda, Docker and NVIDIA Container Toolkit.

## THESE INSTRUCTIONS ARE FOR UBUNTU 24.04 (LATEST)
The commands in this readme are for **Ubuntu 24.04**. You can follow similar steps for other Ubuntu versions however the commands will be slightly different. Instructions last updated **Oct 29 2024**.

## 1. GPU Drivers

You can install drivers through the Additional Drivers application in the Ubuntu menu.

Another option is to install drivers by executing the following commands in the terminal.
```bash
sudo apt install nvidia-driver-550
```

Once drivers are installed you will need to reboot the system.
```bash
sudo reboot
```

After you can verify the installation with the following commands:
```bash
nvidia-smi
```

You should see something that looks like this:
```bash
Thu Feb 29 07:07:23 2024       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 545.23.08              Driver Version: 545.23.08    CUDA Version: 12.3     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA RTX A6000               On  | 00000000:0A:00.0  On |                  Off |
| 30%   30C    P8              26W / 300W |    441MiB / 49140MiB |      4%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1950      G   /usr/lib/xorg/Xorg                          212MiB |
|    0   N/A  N/A      2083      G   /usr/bin/gnome-shell                        146MiB |
+---------------------------------------------------------------------------------------+
```



## THESE INSTRUCTIONS ARE FOR UBUNTU 22.04 (OLD)
The commands in this readme are for **Ubuntu 22.04**. You can follow similar steps for other Ubuntu versions however the commands will be slightly different. Instructions last updated **Mar 3 2024**.

## 1. GPU Drivers

You can install drivers through the Additional Drivers application in the Ubuntu menu.

Another option is to install drivers by executing the following commands in the terminal.
```bash
sudo apt install nvidia-driver-545
```

Once drivers are installed you will need to reboot the system.
```bash
sudo reboot
```

After you can verify the installation with the following commands:
```bash
nvidia-smi
```

You should see something that looks like this:
```bash
Thu Feb 29 07:07:23 2024       
+---------------------------------------------------------------------------------------+
| NVIDIA-SMI 545.23.08              Driver Version: 545.23.08    CUDA Version: 12.3     |
|-----------------------------------------+----------------------+----------------------+
| GPU  Name                 Persistence-M | Bus-Id        Disp.A | Volatile Uncorr. ECC |
| Fan  Temp   Perf          Pwr:Usage/Cap |         Memory-Usage | GPU-Util  Compute M. |
|                                         |                      |               MIG M. |
|=========================================+======================+======================|
|   0  NVIDIA RTX A6000               On  | 00000000:0A:00.0  On |                  Off |
| 30%   30C    P8              26W / 300W |    441MiB / 49140MiB |      4%      Default |
|                                         |                      |                  N/A |
+-----------------------------------------+----------------------+----------------------+
                                                                                         
+---------------------------------------------------------------------------------------+
| Processes:                                                                            |
|  GPU   GI   CI        PID   Type   Process name                            GPU Memory |
|        ID   ID                                                             Usage      |
|=======================================================================================|
|    0   N/A  N/A      1950      G   /usr/lib/xorg/Xorg                          212MiB |
|    0   N/A  N/A      2083      G   /usr/bin/gnome-shell                        146MiB |
+---------------------------------------------------------------------------------------+
```

## 2. Cuda Toolkit 12-3 deb (local)

Install the CUDA toolkit system wide. You can find the installation guide on the NVIDIA website:

[CUDA Installation Guide](https://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html#)

The toolkit can be downloaded from the following link:

[CUDA Downloads](https://developer.nvidia.com/cuda-downloads?target_os=Linux&target_arch=x86_64&Distribution=Ubuntu&target_version=20.04&target_type=deb_local)

After downloading, run the following commands:

```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2004/x86_64/cuda-ubuntu2004.pin
sudo mv cuda-ubuntu2004.pin /etc/apt/preferences.d/cuda-repository-pin-600
wget https://developer.download.nvidia.com/compute/cuda/12.3.0/local_installers/cuda-repo-ubuntu2004-12-3-local_12.3.0-545.23.06-1_amd64.deb
sudo dpkg -i cuda-repo-ubuntu2004-12-3-local_12.3.0-545.23.06-1_amd64.deb
sudo cp /var/cuda-repo-ubuntu2004-12-3-local/cuda-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cuda-toolkit-12-3
```

After installation, remember to add CUDA paths to your profile as shown in the Post-Installation Actions section of the CUDA Installation Guide:

```bash
export PATH=/usr/local/cuda-12.3/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.3/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

Reboot the system:
```bash
sudo reboot
```
and verify the installation with 
```bash
nvcc --version
```

## 3. NVIDIA cuDNN

You need an NVIDIA account to download cuDNN. You can download it from the following links:

[NVIDIA cuDNN](https://developer.nvidia.com/cudnn)

[cuDNN Download](https://developer.nvidia.com/rdp/cudnn-download)

And find the Installation Guide here:

[Installation Guide](https://docs.nvidia.com/deeplearning/cudnn/install-guide/index.html)

Refer to the Debian Local Installation section in the guide.

Or just do the following:
```bash
wget https://developer.download.nvidia.com/compute/cudnn/9.0.0/local_installers/cudnn-local-repo-ubuntu2204-9.0.0_1.0-1_amd64.deb
sudo dpkg -i cudnn-local-repo-ubuntu2204-9.0.0_1.0-1_amd64.deb
sudo cp /var/cudnn-local-repo-ubuntu2204-9.0.0/cudnn-*-keyring.gpg /usr/share/keyrings/
sudo apt-get update
sudo apt-get -y install cudnn
```
## 4. Miniconda

To install Miniconda, start by downloading it via the command:

```bash
wget https://repo.anaconda.com/miniconda/Miniconda3-latest-Linux-x86_64.sh
```

Install it using the command:

```bash
bash Miniconda3-latest-Linux-x86_64.sh
```
### 5. Customize Desktop
```bash
sudo apt install plymouth-themes
```
```bash
sudo plymouth --hide-splash
```
```bash
sudo apt install ubuntu-gnome-desktop
```
```bash
sudo dpkg-reconfigure gdm3
```
```bash
sudo apt install gnome-tweaks
```
logout and back in using GNOME desktop
```bash
sudo apt remove gnome-shell-extension-ubuntu-dock
```
remove login screen
```bash
sudo apt install dconf-editor
gsettings set org.gnome.desktop.interface clock-show-date false
```
