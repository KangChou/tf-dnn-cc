

```shell
sudo apt-get update && sudo apt-get upgrade
apt install -f 
apt-get update --fix-missing

sudo apt-get install curl wget
sudo apt-get install openjdk-8-jdk 
echo "deb [arch=amd64] http://storage.googleapis.com/bazel-apt stable jdk1.8" | sudo tee /etc/apt/sources.list.d/bazel.list 
curl https://bazel.build/bazel-release.pub.gpg | sudo apt-key add - 
sudo apt-get update 
wget https://github.com/bazelbuild/bazel/releases/download/0.26.1/bazel-0.26.1-installer-linux-x86_64.sh
chmod +x bazel-0.26.0-installer-linux-x86_64.sh 
./bazel-0.26.0-installer-linux-x86_64.sh --user

sudo apt-get install autoconf automake libtool 
git clone https://github.com/google/protobuf.git 
cd protobuf 
git checkout v3.8.0 
./autogen.sh 
./configure 
make 
make check 
sudo make install

wget http://bitbucket.org/eigen/eigen/get/3.3.4.tar.bz2
tar -xvjf 3.3.4.tar.bz2
cd eigen-folder
mkdir build
cd build
cmake ..
make
sudo make install



git clone https://github.com/tensorflow/tensorflow.git 
cd tensorflow 
git pull origin master 
git fetch --tags 
git checkout v1.15.0 
./configure  # N

bazel build --config=opt //tensorflow:libtensorflow_cc.so# 

# mkdir Example_tensorflow
# export TENSORFLOW_DIR=/root/tensorflow
# cd Example_tensorflow/
# mkdir -p include/third_party
# cp -r $TENSORFLOW_DIR/tensorflow include/third_party/
# cp -r $TENSORFLOW_DIR/bazel-genfiles/tensorflow include/third_party/
# cp -r $TENSORFLOW_DIR/third_party/eigen3 include/third_party/
# cp -r /data/protobuf/src/google include/third_party/
# cp -r /data/eigen-folder/. include/third_party/eigen3/
# cp -r include/third_party/eigen3/Eigen include/third_party/


cd include/
git clone https://github.com/google/nsync.git
cd ~
cd Example_tensorflow
mkdir lib
cp -r $TENSORFLOW_DIR/bazel-bin/tensorflow/libtensorflow_cc.so /root/Example_tensorflow/lib
sudo ldconfig

cp $TENSORFLOW_DIR/tensorflow/examples/label_image/main.cc .
cp -r $TENSORFLOW_DIR/tensorflow/examples/label_image/data .

cd data
wget https://storage.googleapis.com/download.tensorflow.org/models/inception_v3_2016_08_28_frozen.pb.tar.gz
tar -xzvf inception_v3_2016_08_28_frozen.pb.tar.gz

cd ~
cd Example_tensorflow

touch Makefile


apt-get update --fix-missing
apt install -f 
sudo apt-get update && sudo apt-get upgrade

# https://vlearningit.wordpress.com/installation/compile-tensorflow-c-from-the-source-in-ubuntu-18-04-lts/


```
