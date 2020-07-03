## This blog is about deploying a distributed system for deep learning

Environment
  Centos 8.1 | NVIDIA TESLA T4 TENSOR CORE GPU | Horovod | Tensorflow-gpu-2.1.0 

### Download drivers for NVIDIA & system compiler software

```markdown
# 1 Software & package & configuration

  yum update
  
  sudo yum install "kernel-devel-uname-r == $(uname -r)"
  
- we must insure that kernel-devel match the kernel version
- if it shows that no match kernel-devel, reboot then try again

  yum install epel-release
  
  yum install gcc gcc-c++ dkms make
  
  echo -e "blacklist nouveau\noptions nouveau modeset=0" > /etc/modprobe.d/blacklist.conf
  
- forbid nouveau module load

  mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
  
  dracut /boot/initramfs-$(uname -r).img $(uname -r)
  
  reboot
  
# 2 Install NVIDIA drivers

- download the driver from NVIDIA and make sure it correspond with your GPU device

  chmod u+x NVIDIA-xxxxxx.run
  
  ./NVIDIA-xxxxx.run --kernel-source-path=/usr/src/kernels/xxxxxx
  
- use `nvidia-smi` to show GPU status

```

### Install CUDA & cuDNN & nccl 

```markdown
# 1 CUDA

_we can use the runfile way to install CUDA_

  wget http://developer.download.nvidia.com/compute/cuda/10.1/Prod/local_installers/cuda_10.1.243_418.87.00_linux.run

  sudo sh cuda_10.1.243_418.87.00_linux.run

_add cuda-10.1/bin & cuda-10.1/lib64 to PATH ,then use `nvcc --version` to check_

# 2 cuDNN

  tar -xzvf xxxxx.tgz

  sudo cp cuda/include/cudnn.h /usr/local/cuda/include

  sudo cp cuda/lib64/libcudnn* /usr/local/cuda/lib64

  sudo chmod a+r /usr/local/cuda/include/cudnn.h /usr/local/cuda/lib64/libcudnn*

# 3 nccl

_download local installers(x86) —— Local installer for RedHat/CentOS 7_

  rpm -i nccl-repo-rhel7-2.7.5-ga-cuda10.1-1-1.x86_64.rpm

  sudo yum install libnccl-2.7.5-1+cuda10.1 libnccl-devel-2.7.5-1+cuda10.1 libnccl-static-2.7.5-1+cuda10.1

```


**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

