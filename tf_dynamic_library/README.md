
# 获取TF2 C++动态库 进行调用
https://github.com/jinay1991/spleeter/releases/download/v2.3/libtensorflow_cc-2.3.0-linux.tar.gz

https://github.com/jinay1991/spleeter/releases/download/v1.2/libtensorflow_cc-2.1.0-linux.tar.gz


解压到tensorflow目录下：
```shell
|-- tensorflow
|   |-- include
|   `-- lib
`-- tf_test.cc
```

# 开发环境
- ubuntu18.04
- ananconda3+python3.6
- docker pull ckck2021/ubuntu18.04:py36-aliyun

docker 使用方法：https://blog.csdn.net/weixin_41194129/article/details/113832695?spm=1001.2014.3001.5502

由于该环境是使用docker+ubuntu配置的但是未安装glibc-2.27，所有需要安装glibc-2.27

方法一：
```SHELL
sudo apt-get install glibc-source
sudo apt-get upgrade glibc-source
```

方法二：
```shell
apt-get update --fix-missing
sudo apt-get install gawk bison
curl -O http://ftp.gnu.org/gnu/glibc/glibc-2.27.tar.gz
tar zxf glibc-2.27.tar.gz 
cd glibc-2.28/
mkdir build 
cd build/
../configure --prefix=/usr/local/glibc-2.27
make -j8
sudo make install
apt-get update --fix-missing
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/glibc-2.27/lib
ldd --version

```

# tf_c++测试demo
```
#include <stdio.h>
#include "tensorflow/c/c_api.h"

int main() {
      printf("Hello from TensorFlow C library version %s\n", TF_Version());
      return 0;
}


```
编译执行：
```
g++ tf_test.cc -I./tensorflow/include -L./tensorflow/lib -ltensorflow_cc -std=c++11 -o tf_test 
./tf_test

```




