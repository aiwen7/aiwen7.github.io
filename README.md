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


**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

