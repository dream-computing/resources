# Ubuntu 24.04 Install Guide
## Last Updated: 2025 June 30

## Basic Ubuntu Packages
```bash
sudo apt install curl
```

## Install NVIDIA Drivers
You need to install the Open drivers for 50 series GPUs. https://developer.nvidia.com/datacenter-driver-downloads
```bash
wget https://developer.download.nvidia.com/compute/cuda/repos/ubuntu2404/x86_64/cuda-keyring_1.1-1_all.deb
sudo dpkg -i cuda-keyring_1.1-1_all.deb
sudo apt-get update
sudo apt-get install -y nvidia-open-570
```

## Tailscale Install
```bash
curl -fsSL https://tailscale.com/install.sh | sh
sudo tailscale up
```
