
Ubuntu18.04 安装 glibc-2.27
```shell
wget http://ftp.gnu.org/gnu/glibc/glibc-2.27.tar.gz
tar zxvf glibc-2.27.tar.gz
cd glibc-2.27
mkdir build
cd build
../configure --prefix=/usr --disable-profile --enable-add-ons --with-headers=/usr/include --with-binutils=/usr/bin 
&& make && make install

查看
strings /lib/x86_64-linux-gnu/libm.so.6|grep GLIBC_

```
