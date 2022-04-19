
# 阿里云镜像源构建tensorflow 1.15 c++环境

## ENV 

- Ubuntu 18.04.6 LTS \n \l
- cuda111.1+cudnn8
- conda+python3.6
-  protoc --version   # ----> libprotoc 2.6.1
-  bazel 0.26.1
-  tensorflow 1.15

```shell
bazel version
Build label: 0.26.1
Build target: bazel-out/k8-opt/bin/src/main/java/com/google/devtools/build/lib/bazel/BazelServer_deploy.jar
Build time: Thu Jun 6 11:05:05 2019 (1559819105)
Build timestamp: 1559819105
Build timestamp as int: 1559819105
```

## 步骤

```shell
apt-get update --fix-missing

# 阿里云源
sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list && \
sed -i 's/security.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list       
apt-get update --fix-missing

# Installation of general dependencies
apt-get install -y build-essential clang-format clang-tidy clang git git-lfs wget curl gnupg openjdk-11-jdk openjdk-11-jre lcov

# Installation of FFMPEG

apt-get install -y libavcodec-dev libavformat-dev libavfilter-dev libavdevice-dev libswresample-dev libswscale-dev ffmpeg


# 安装指定的0.26.1版本
wget https://github.com/bazelbuild/bazel/releases/download/0.26.1/bazel-0.26.1-installer-linux-x86_64.sh
# wget https://github.com/bazelbuild/bazel/releases/download/0.19.0/bazel-0.19.0-installer-linux-x86_64.sh
# wget https://github.com/bazelbuild/bazel/releases/download/0.22.0/bazel-0.22.0-installer-linux-x86_64.sh
bash bazel-0.26.1-installer-linux-x86_64.sh
sudo apt-get upgrade bazel
# # 以下默认安装5.1版本
# Installation
curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > bazel.gpg
mv bazel.gpg /etc/apt/trusted.gpg.d/
echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list

# apt-get update && apt-get install -y bazel

# # 报错记录error
# #The following signatures couldn't be verified because the public key is not available: NO_PUBKEY 3D5919B448457EE0 E: The repository 'https://storage.googleapis.com/bazel-apt stable InRelease' is not signed.

# # 解决方法solv: 
# apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 3D5919B448457EE0
# # reinstall: 
# apt-get update && apt-get install -y bazel

# 查看安装版本
bazel version


# 安装完成之后，还需要在bashrc里面添加环境变量
vim ~/.bashrc
export PATH="$PATH:$HOME/bin"
source ~/.bashrc


git clone https://github.com/tensorflow/tensorflow.git

cd tensorflow

git checkout r1.15



# sudo apt-get install autoconf automake libtool
wget -O protobuf.tar.gz https://github.com/protocolbuffers/protobuf/releases/download/v3.6.1/protobuf-python-3.6.1.tar.gz \ 
&& tar xvf protobuf.tar.gz
mkdir protobuf_bin
cd protobuf-3.6.1
./autogen.sh
# ./autogen.sh && ./configure
# make && make install && ldconfig
# cd python
# python setup.py build --cpp_implementa

./configure --prefix=$HOME/protobuf_bin #(自己新建的目录)
make -j4
make install 

vim ~/.bashrc
export PATH=$PATH:$HOME/protobuf_bin/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$HOME/protobuf_bin/lib
export CPLUS_INCLUDE_PATH="/root/protobuf_bin/include"
source ~/.bashrc
which protoc
protoc --version
# 如果想要卸载protobuf
cd protobuf-3.6.1/
make uninstall

cd tensorflow
./configure

# 按照下面图片的方式执行，只要CUDA support选择Y，其他基本上都是默认和N，其中默认是10.0，
# 如果不是在后面写上对应的版本，我cudnn是7.6.3，默认是7，直接默认也是OK的。设置完成之后需要编译

# // 有显卡
bazel build --config=opt --config=cuda //tensorflow:libtensorflow_cc.so
# // 无显卡
bazel build --config=opt //tensorflow:libtensorflow_cc.so


```
![image](https://user-images.githubusercontent.com/36963108/164009709-66eaa68b-61e6-4bb0-9604-6864c6b437bc.png)

构建环境的时候需要多试几次就成功了：bazel build --config=opt //tensorflow:libtensorflow_cc.so
![image](https://user-images.githubusercontent.com/36963108/164010125-2db5975a-a4bb-4785-8b37-1fb717f007ac.png)





