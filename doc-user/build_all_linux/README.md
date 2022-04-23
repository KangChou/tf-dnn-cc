
# 解决构建过程中的问题
## 1、build_all_linux加载问题
只需要更换很久不存在的url就可以了：build_all_linux.sh，路径：tensorflow/contrib/makefile/build_all_linux.sh
```shell

#!/bin/bash
# Copyright 2015 The TensorFlow Authors. All Rights Reserved.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#     http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
# ==============================================================================

set -e

DOWNLOADS_DIR=tensorflow/contrib/makefile/downloads
BZL_FILE_PATH=tensorflow/workspace.bzl

# Ensure it is being run from repo root
if [ ! -f $BZL_FILE_PATH ]; then
  echo "Could not find ${BZL_FILE_PATH}":
  echo "Likely you are not running this from the root directory of the repository.";
  exit 1;
fi

# EIGEN_URL="$(grep -o 'https://bitbucket.org/eigen/eigen/get/.*tar\.gz' "${BZL_FILE_PATH}" | grep -v mirror.bazel | head -n1)"
EIGEN_URL="https://gitlab.com/libeigen/eigen/-/archive/3.4.0/eigen-3.4.0.ta​​r.gz"
# EIGEN_URL="$(grep -o 'https://storage.googleapis.com/mirror.tensorflow.org/gitlab.com/libeigen/eigen/.*tar.gz' "${BZL_FILE_PATH}")"

GEMMLOWP_URL="$(grep -o 'https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/gemmlowp/.*zip' "${BZL_FILE_PATH}" | head -n1)"

# GOOGLETEST_URL="https://github.com/google/googletest/archive/release-1.8.0.tar.gz"
GOOGLETEST_URL="https://github.com/google/googletest/archive/refs/tags/release-1.8.0.tar.gz"

NSYNC_URL="$(grep -o 'https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/nsync/.*tar\.gz' "${BZL_FILE_PATH}" | head -n1)"

# Note: The protobuf repo needs to be cloned due to its submodules.
# These variables contain the GitHub repo and the sha, from `tensorflow/workspace.bzl`,
# from which to clone it from and checkout to.
readonly PROTOBUF_REPO="https://github.com/protocolbuffers/protobuf.git"
readonly PROTOBUF_TAG="$(grep -o 'https://github.com/protocolbuffers/protobuf/archive/.*tarr\.gz' "${BZL_FILE_PATH}" | head -n1 | awk '{print substr($0, index($0, "archive") + 8, index($0, "tar") - index($0, "archive") - 9) }')"

# TODO (yongtang): Replace the following with 'https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/re2/.*tar\.gz' once
# the archive has been propagated in mirror.tensorflow.org.

# RE2_URL="$(grep -o 'https://github.com/google/re2/.*tar\.gz' "${BZL_FILE_PATH}" | head -n1)"
RE2_URL="https://github.com/google/re2/archive/refs/tags/2021-09-01.tar.gz"

FFT2D_URL="$(grep -o 'http.*fft2d\.tgz' "${BZL_FILE_PATH}" | grep -v bazel-mirror | head -n1)"
DOUBLE_CONVERSION_URL="$(grep -o "https.*google/double-conversion.*\.zip" "${BZL_FILE_PATH}" | head -n1)"

# ABSL_URL="$(grep -o 'https://github.com/abseil/abseil-cpp/.*tar.gz' "${BZL_FILE_PATH}" | head -n1)"
ABSL_URL="https://github.com/abseil/abseil-cpp/archive/refs/tags/20200225.2.tar.gz"

CUB_URL="$(grep -o 'https.*cub/archive.*zip' "${BZL_FILE_PATH}" | grep -v mirror.bazel | head -n1)"

# Required for TensorFlow Lite Flex runtime.
FARMHASH_URL="https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/farmhash/archive/816a4ae622e964763ca0862d9dbd19324a1eaf45.tar.gz"
FLATBUFFERS_URL="https://storage.googleapis.com/mirror.tensorflow.org/github.com/google/flatbuffers/archive/v1.11.0.tar.gz"
```

## 2、icu编译问题的解决方法

```shell
wget https://github.com/unicode-org/icu/archive/release-62-1.tar.gz #记得先这个git checkout v1.15.0
下载下来，然后因为下载下来后的文件名是icu-release-62-1.tar.gz，所以我改为release-62-1.tar.gz
tensorflow根目录/third_party/icu/workspace.bzl，在urls = []中添加：

"file://data/TF/tensorflow/third_party/icu/release-62-1.tar.gz",
```
## 3、protobuf编译问题解决方法

```
git clone https://github.com/google/protobuf.git 
cd protobuf 
git checkout v3.8.0 
git submodule update --init --recursive
./autogen.sh 
./configure 
make 
make check 
sudo make install
protoc --version
# 补充：configure: WARNING: no configuration information is in third_party/googletest
执行如下命令，可以检测并补全third_party目录下所有文件，包括但不限于googletest，是最简单的方式：
git submodule update --init --recursive
也可以直接通过如下完成：
从 https://github.com/google/googletest/releases 获取源文件
如 https://github.com/google/googletest/***.tar.gz
解压并重命名为googletest（即去掉文件夹中的版本号）,并放到 third_party/即可。
方案一： 最简单的方式是在/etc/profile 或 .bashrc 等相关文件中加入：
 export LD_LIBRARY_PATH=/usr/local/lib,
 如：
# vim /etc/profile
# 文件最下面添加：
export LD_LIBRARY_PATH=/usr/local/lib 
# source /etc/profile 
方案二： 但使用过程中发现仅当次有效，注销系统后又失效了，故采用了如下方式：
通过 sudo vim /etc/ld.so.conf.d/libprotobuf.conf  
新建【/etc/ld.so.conf.d/libprotobuf.conf】 ，内容如下：
/usr/local/lib
执行命令 sudo ldconfig
/etc/profile 添加路径
 # vim /etc/profile
 export LD_LIBRARY_PATH=/usr/local/lib:$LD_LIBRARY_PATH
 # source /etc/profile

```
