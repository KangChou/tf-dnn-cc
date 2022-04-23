
# tensorflow c++ 调用GPU训练

由于TF安装路径的不同，所以我在以下源码的基础上修改了头文件路径，详细请看上述源码。

环境推荐:docker pull baligica/tensorflow_1.15.2_cuda_10.1_opencvdeps

教程参考文档：https://mp.csdn.net/mp_blog/creation/success/124159957

源码来自：https://github.com/theflofly/dnn_tensorflow_cpp

当然你也可以使用bazel工具来编译程序：BUILD

```shell
load("//tensorflow:tensorflow.bzl", "tf_cc_binary")

tf_cc_binary(
    name = "model",
    srcs = [
        "model.cc",
        "data_set.h",
        "data_set.cc"
    ],
    deps = [
        "//tensorflow/cc:gradients",
        "//tensorflow/cc:grad_ops",
        "//tensorflow/cc:cc_ops",
        "//tensorflow/cc:client_session",
        "//tensorflow/core:tensorflow"
    ],
    data = ["normalized_car_features.csv"]
)

```



工具下载：https://fossies.org/linux/misc/
https://www.tensorflow.org/versions/r1.15/api_docs/cc
https://www.tensorflow.org/versions

http://www.atyun.com/14129.html

其他数据集
https://www.cxyread.cc/read/K3ZnK3x9ZVQ.html



参考学习:


https://towardsdatascience.com/applied-deep-learning-part-4-convolutional-neural-networks-584bc134c1e2

https://towardsdatascience.com/applied-deep-learning-part-1-artificial-neural-networks-d7834f67a4f6


This project is a simple deep neural network trained using only TensorFlow C++. 

This is the support code of this [blog post](https://matrices.io/training-a-deep-neural-network-using-only-tensorflow-c/).

https://medium.com/itnext/creating-a-tensorflow-dnn-in-c-part-1-54ce69bbd586

https://towardsdatascience.com/creating-a-tensorflow-cnn-in-c-part-2-eea0de9dcada

https://medium.com/@htmbx6/build-and-train-neural-network-with-tensorflow-c-f13f22d3c5b6
