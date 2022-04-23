
# 解决构建过程中的问题
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
