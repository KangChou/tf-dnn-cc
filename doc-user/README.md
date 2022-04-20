
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
apt-get install autoconf automake libtool curl make g++ unzip
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
echo "deb [arch=amd64] https://storage.googleapis.com/bazel-apt stable jdk1.8" | tee /etc/apt/sources.list.d/bazel.list
curl -fsSL https://bazel.build/bazel-release.pub.gpg | gpg --dearmor > bazel.gpg
mv bazel.gpg /etc/apt/trusted.gpg.d/


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

mkdir $HOME/protobuf_bin  #新建安装路径
cd protobuf-3.6.1
./autogen.sh
# ./autogen.sh && ./configure
# make && make install && ldconfig
# cd python
# python setup.py build --cpp_implementa

./configure --prefix=$HOME/protobuf_bin #(自己新建的目录)
make -j4
make install 

#./configure
#make
#make check
#make install

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

或者：

	cd protobuf-xxx
	./autogen.sh
	./configure
	make			// 耗时较长
	make check		// 非必须
	sudo make install
	sudo ldconfig
 
 如何卸载：protobuf的卸载
sudo apt-get remove libprotobuf-dev
which protoc    // 查看可执行文件路径
sudo rm /usr/local/bin/protoc           // 删执行文件                                       //（也可能是/usr/bin/protoc）
sudo rm -rf /usr/local/include/google   //头文件
sudo rm -rf /usr/local/lib/libproto*    //库文件
 


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

## 安装abseil
解决如下报错：
```shell
/tensorflow/core/lib/core/errors.h:21:10: fatal error: absl/strings/str_join.h: No such file or directory
 #include "absl/strings/str_join.h"
          ^~~~~~~~~~~~~~~~~~~~~~~~~
```
可以参考：https://abseil.io/docs/cpp/quickstart \
https://github.com/abseil/abseil-cpp/blob/master/CMake/README.md \
下载所需包：git clone https://github.com/abseil/abseil-cpp.git \
编译安装：

```shell
cd abseil-cpp/CMakeLists.txt  然后添加：add_definitions(-std=c++11)  set(CMAKE_CXX_STANDARD 11)

# and then issuing `yum install cmake3` on the command line.
cmake_minimum_required(VERSION 3.5)
set(CMAKE_CXX_STANDARD 11)
add_definitions(-std=c++11)


cd abseil-cpp
mkdir build && mkdir install && cd build
# 安装到指定文件路径，这个方法只是理解安装逻辑，最后还有移动到/usr/目录下，很麻烦，这里只是学习使用
cmake -DCMAKE_INSTALL_PREFIX=../install/ DABSL_PROPAGATE_CXX_STD=ON -DABSL_PROPAGATE_CXX_STD=ON  -DCMAKE_CXX_STANDARD=11 ..  
make
make install

```
### 头文件迁移
头文件就在install/目录中，只要将文件夹include中的absl文件夹复制到/usr/include目录即可。

cp -r install/include/absl/ /usr/include/


### 静态库文件生成
默认产生了一大堆静态库文件，使用不方便，所以我们将所有的静态库打包成一个libabsl.a静态库。
find ./ -name "*.o" | xargs ar cr libabsl.a
进入刚才创建的build目录，执行上面的命令，会生成一个静态库libabsl.a。

cp libabsl.a /usr/lib

将生成的库复制到系统路径，这样abseil库就安装好了。

```shell

abseil-cpp/build# find ./ -name "*.o" | xargs ar cr libabsl.a
root@ovo:~/cudnn_samples_v8/tf_cc_demo/abseil-cpp/build# du -sh *
362M    absl
4.0K    abslConfig.cmake
407M    bin
36K     CMakeCache.txt
2.1M    CMakeFiles
4.0K    cmake_install.cmake
4.0K    CTestTestfile.cmake
4.0K    DartConfiguration.tcl
3.6M    googletest-build
1.2M    googletest-external
4.4M    googletest-src
28K     install_manifest.txt
3.9M    lib
365M    libabsl.a
172K    Makefile
8.0K    Testing
root@ovo:~/cudnn_samples_v8/tf_cc_demo/abseil-cpp/build# 
root@ovo:~/cudnn_samples_v8/tf_cc_demo/abseil-cpp/build# cp libabsl.a /usr/lib

```


使用下面的安装方法直接默认安装就可以了：
```shell
# rm -rf build 
# mkdir build && mkdir install && cd build
mkdir build && cd build
# # cmake -DCMAKE_INSTALL_PREFIX=../install/ DABSL_PROPAGATE_CXX_STD=ON -DABSL_PROPAGATE_CXX_STD=ON  -DCMAKE_CXX_STANDARD=11 ..  
cmake DABSL_PROPAGATE_CXX_STD=ON -DABSL_BUILD_TESTING=ON -DABSL_USE_GOOGLETEST_HEAD=ON -DCMAKE_CXX_STANDARD=11 ..  
make
make install
apt update
 
注意：静态文件的生成同上步骤。


```
![image](https://user-images.githubusercontent.com/36963108/164135747-6deae382-7010-4f6d-92cf-a6c1823e9125.png)



### demo测试
```cpp
// g++ main.cc -labsl -o main && ./main
#include <iostream>
#include <absl/strings/string_view.h>

using namespace std;

int main()
{
    absl::string_view sv = "hello world";

    cout<<sv<<endl;

    return 0;
}



```
### 解决port_def.inc编译的问题
原因：google/protobuf/port_def.inc包含在 protoc 版本 >= 3.7.0 生成的标头中。 \
因此需要重新安装大于3.6的版本：#https://github.com/protocolbuffers/protobuf/archive/refs/tags/v3.6.0.tar.gz
编译方法同上。


```
configure: WARNING: no configuration information is in third_party/googletest
```
需要下载googletest，下载地址：https://github.com/google/googletest/releases，解压后放在./protobuf-3.10.1/third_party/googletest，然后执行./autogen.sh

wget https://github.com/google/googletest/archive/refs/tags/release-1.11.0.tar.gz


如果不用了，卸载方法如下：

用命令找到安装目录：which protoc

删除安装目录的安装包，例如下面的命令：rm /usr/bin/protoc  && rm -rf /root/protobuf_bin/


补充：一键安装

sudo apt install protobuf-compiler
尝试 sudo apt-get remove protobuf-compiler 而不是通过 apt-get install 重新安装





