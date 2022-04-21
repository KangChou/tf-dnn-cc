 Could NOT find Boost
-- Could NOT find GOOGLEHASH (missing: GOOGLEHASH_INCLUDES GOOGLEHASH_COMPILE) 
-- Could NOT find ADOLC (missing: ADOLC_INCLUDES ADOLC_LIBRARIES) 
-- Could NOT find MPFR (missing: MPFR_INCLUDES MPFR_LIBRARIES MPFR_VERSION_OK) (Required is at least version "2.3.0")
-- Could NOT find GMP (missing: GMP_INCLUDES GMP_LIBRARIES) 
-- Could NOT find PkgConfig (missing: PKG_CONFIG_EXECUTABLE) 
-- Could NOT find FFTW (missing: FFTW_INCLUDES FFTW_LIBRARIES) 
-- Could NOT find OpenGL (missing: OPENGL_opengl_LIBRARY OPENGL_glx_LIBRARY OPENGL_INCLUDE_DIR) 
-- Could NOT find GLUT (missing: GLUT_glut_LIBRARY GLUT_INCLUDE_DIR) 
CUDA_TOOLKIT_ROOT_DIR not found or specified
-- Could NOT find CUDA (missing: CUDA_TOOLKIT_ROOT_DIR CUDA_NVCC_EXECUTABLE CUDA_INCLUDE_DIRS CUDA_CUDART_LIBRARY) (Required is at least version "7.0")
-- Found unsuitable Qt version "" from NOTFOUND
-- Qt4 not found, so disabling the mandelbrot and opengl demos
-- Could NOT find CHOLMOD (missing: CHOLMOD_INCLUDES CHOLMOD_LIBRARIES) 
-- Could NOT find UMFPACK (missing: UMFPACK_INCLUDES UMFPACK_LIBRARIES) 
-- Could NOT find SUPERLU (missing: SUPERLU_INCLUDES SUPERLU_LIBRARIES SUPERLU_VERSION_OK) 
-- Could NOT find PkgConfig (missing: PKG_CONFIG_EXECUTABLE) 
-- Could NOT find BLAS (missing: BLAS_LIBRARY_DIRS) 
-- Could NOT find MPI_C (missing: MPI_C_LIB_NAMES MPI_C_HEADER_DIR MPI_C_WORKS) 
-- Could NOT find MPI_CXX (missing: MPI_CXX_LIB_NAMES MPI_CXX_HEADER_DIR MPI_CXX_WORKS) 
-- Could NOT find MPI (missing: MPI_C_FOUND MPI_CXX_FOUND) 

apt install libboost-all-dev


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


wget https://gitlab.com/libeigen/eigen/-/archive/3.3.7/eigen-3.3.7.tar.gz $ tar -xzvf eigen-3.3.7.tar.gz
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
