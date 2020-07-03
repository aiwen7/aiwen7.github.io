## This blog is about deploying a distributed system for deep learning

Environment
  Centos 8.1 | NVIDIA TESLA T4 TENSOR CORE GPU | Horovod | Tensorflow-gpu-2.1.0 

### Download drivers for NVIDIA & system compiler software

```markdown
# 1 Software & package & configuration

  yum update
  
  sudo yum install "kernel-devel-uname-r == $(uname -r)"
  
_we must insure that kernel-devel match the kernel version_
_if it shows that no match kernel-devel, reboot then try again_

  yum install epel-release
  
  yum install gcc gcc-c++ dkms make
  
  echo -e "blacklist nouveau\noptions nouveau modeset=0" > /etc/modprobe.d/blacklist.conf
  
_forbid nouveau module load_

  mv /boot/initramfs-$(uname -r).img /boot/initramfs-$(uname -r).img.bak
  
  dracut /boot/initramfs-$(uname -r).img $(uname -r)
  
  reboot
  
# 2 Install NVIDIA drivers

_download the driver from NVIDIA and make sure it correspond with your GPU device_

  chmod u+x NVIDIA-xxxxxx.run
  
  ./NVIDIA-xxxxx.run --kernel-source-path=/usr/src/kernels/xxxxxx
  
_use `nvidia-smi` to show GPU status_

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

### Install Python & Anaconda & Tensorflow

```markdown
# 1 Python

[a method for python3 installation](https://www.cnblogs.com/malukang/p/12610914.html)

_add Python3.x.x/bin to PATH ,then use `python3 -v` to check_

# 2 Anaconda 

_download install file in Anaconda_

  bash Anaconda3-2020.02-Linux-x86_64.sh

_add export "PATH=$PATH:/root/anaconda3/bin" in ~/.bashrc if you can't use `conda` command_

# 3 Tensorflow

  conda create -n tf-gpu-2.1.0 python=3.7

  conda activate tf-gpu-2.1.0

  conda install tensorflow-gpu==2.1.0

# 4 TensorRT

  tar xzvf TensorRT-6.0.1.5.CentOS-7.6.x86_64-gnu.cuda-10.1.cudnn7.6.tar.gz

  sudo cp -r ./TensorRT-6.0.1.5/ /usr/local/
  
  sudo chmod a+r /usr/local/TensorRT-6.0.1.5
  
  vim ~/.bashrc
  
  export “LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/TensorRT-6.0.1.5/lib”
  
  source ~/.bashrc

_conda activate your-conda-environment and install the following .whl_
    
  cd ./TensorRT-6.0.1.5/python/
  
  pip3 install tensorrt-6.0.1.5-cp37-none-linux_x86_64.whl
  
  cd ../uff
  
  pip3 install uff-0.6.5-py2-py3-none-any.whl


```

### How to run jupyter notebook in my local brower(optional)

```markdown

[a method ](https://blog.csdn.net/weixin_41576149/article/details/104891612)

_use Xshell,after configurating the ssh tunnel,click connect to ssh tunnel to open a new window

conda activate your-conda-environment and input `jupyter notebook --allow-root`

then you get some output log and find a ip address like `http://127.0.0.1:8888/?token=xxxxxx`

change 8888 to `listenport` that you set and put this address on you brower_

```


### Install MPI & Horovod 

### configurate multi-machine to use SHH without password

```markdown

_add ip and hostname in `/etc/hosts` on every machines

for every machines :_

  ssh-keygen -trsa
  
	cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
  
	chmod 600 ~/.ssh/authorized_keys
  
	ssh-copy-id -i /root/.ssh/id_rsa.pub -p 22 root@nodex
  
	ssh root@nodex

```

### install NFS File sharing system

```markdown

_For server

/etc/exports add /root/share xx.xx.x.0/24(rw,no_root_squash,sync)_

[a method ](https://blog.csdn.net/jinkx0325/article/details/96278071)

_For every client if you get a error when use horovod to train_

  yum install -y iptables
  
  yum update iptables
  
  yum install iptables-services
  
	iptables -A INPUT -p tcp -s xxx.xx.x.xx -j ACCEPT
  
	service iptables save
  
	systemctl enable iptables.service
  
	systemctl start iptables.service

_use `cat /var/log/messages | grep mount` check where cause the problem_

  
```
