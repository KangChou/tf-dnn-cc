
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

# 由于没有使用gpu版本的，所以./configure的过程中都是选择N,因此此C++TF配置的CPU版本
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

编译的TF动态库为：
![image](https://user-images.githubusercontent.com/36963108/164021286-b493686c-58a0-4b2c-8a4d-c187dc6f6adc.png)


## 安装其他C++依赖包
以上的TF C++构建完成之后，在所在的路径下找到：tensorflow/tensorflow/contrib/makefile# \
注意：build_all_linux.sh这个命令是包含./download_dependencies.sh，但是单独执行./download_dependencies.sh这个命令不成功，该命令执行成功后，在tensorflow-1.15/tensorflow/contrib/makefile目录下：downloads文件夹下存放第三方依赖的一些头文件和静态库，比如absl  cub  double_conversion  eigen  fft2d  gemmlowp  googletest  nsync  re2会自动全部安装。

```shell
sudo apt-get install autoconf automake libtool curl make g++ unzip zlib1g-dev git
bash tensorflow/contrib/makefile/build_all_linux.sh
```

![image](https://user-images.githubusercontent.com/36963108/164013113-1c2bd70a-abec-4ee9-9756-e94b8453d803.png)


```
wget https://github.com/google/googletest/archive/release-1.8.0.tar.gz
wget https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/nsync/archive/1.22.0.tar.gz
wget https://github.com/google/re2/archive/506cfa4bffd060c06ec338ce50ea3468daa6c814.tar.gz
wget https://storage.googleapis.com/mirror.tensorflow.org/bitbucket.org/eigen/eigen/get/49177915a14a.tar.gz
wget https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/gemmlowp/archive/12fed0cd7cfcd9e169bf1925bc3a7a58725fdcc3.zip

mkdir tar_set
mv *.tar.gz ./tar_set/
mv *.zip ./tar_set/
cd tar_set

tar -zxvf xxx.tar.gz 
unzip xxx.zip

# 解压后
/tar_set# ls
1.22.0.tar.gz                                    gemmlowp-12fed0cd7cfcd9e169bf1925bc3a7a58725fdcc3
12fed0cd7cfcd9e169bf1925bc3a7a58725fdcc3.zip     googletest-release-1.8.0
49177915a14a.tar.gz                              nsync-1.22.0
506cfa4bffd060c06ec338ce50ea3468daa6c814.tar.gz  re2-506cfa4bffd060c06ec338ce50ea3468daa6c814
eigen-eigen-49177915a14a                         release-1.8.0.tar.gz
```

## 安装eigen3
以下只安装上述解压后的eigen3。安装方法相同，其他需要用到再自行安装。
```shell
cd eigen-eigen-49177915a14a


vim run.sh:

mkdir build
cd build
cmake ..
make
sudo make install

# 一次性编译安装
bash run.sh


```
![image](https://user-images.githubusercontent.com/36963108/164019792-ef960abc-c9c7-4099-b2c8-fbb6b207de4a.png)

根据部分编译输出，安装的在默认路径：

```shell
-- Installing: /usr/local/include/eigen3/Eigen/SVD
-- Installing: /usr/local/include/eigen3/Eigen/Sparse
-- Installing: /usr/local/include/eigen3/Eigen/SparseCholesky
-- Installing: /usr/local/include/eigen3/Eigen/SparseCore
```

解决如下报错：
```shell
/tensorflow/core/lib/core/errors.h:21:10: fatal error: absl/strings/str_join.h: No such file or directory
 #include "absl/strings/str_join.h"
          ^~~~~~~~~~~~~~~~~~~~~~~~~
```
可以参考：https://abseil.io/docs/cpp/quickstart \
下载所需包：git clone https://github.com/abseil/abseil-cpp \
编译安装：



