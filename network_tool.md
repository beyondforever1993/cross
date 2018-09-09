# socat

socat源码：

[socat链接]: http://www.dest-unreach.org/socat/

依赖库包括：libreadline，openssl，libncurses

## libreadline

常规编译



## openssl

下载链接：

[openssl]: openssl（https://www.openssl.org/source/	"TLS/SSL and crypto library"



```
 1. tar -xvf openssl-1.1.0h.tar.gz 
    cd openssl-1.1.0h
 
 2. 交叉环境准备：
    imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi
 
    imx28：export CC=arm-none-linux-gnueabi-gcc
 	    export CXX=arm-none-linux-gnueabi-g++
 
 3. 修改Makefile注释CC赋值
    #CC= $(CROSS_COMPILE)cc
    
 4. make

提示：不要执行根目录的config 和Configure，会把Makefile搞错（配置成整机平台），直接用解压的Makefile，配置交叉编译环境以及把Makefile中CC强制赋值注释后直接编译。
```



##libncurses

下载链接：http://ftp.gnu.org/pub/gnu/ncurses/

```
cd ~/common/ncurses-6.1
mkdir arm-exe
imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi
./configure --prefix=~/common/ncurses-6.1/arm-exe --host=arm-poky-linux-gnueabi --without-ada --without-cxx --without-cxx-binding --without-manpages --without-progs --without-tack --without-tests
make && make install
```



## socat

```
cd socat-1.7.3.2
mkdir arm-exe
imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi
./configure --host=arm-poky-linux-gnueabi --disable-termios --prefix=/home/genglei/common/socat-1.7.3.2/arm-exe CFLAGS="-I/home/genglei/common/openssl-1.1.0h/include -I/home/genglei/common/readline-7.0/arm-exe/include -I/home/genglei/common/ncurses-6.1/arm-exe/include" LDFLAGS="-L/home/genglei/common/openssl-1.1.0h/lib -L/home/genglei/common/readline-7.0/arm-exe/lib -L/home/genglei/common/ncurses-6.1/arm-exe/lib" CPPFLAGS="-I/home/genglei/common/openssl-1.1.0h.bak/include -I/home/genglei/common/readline-7.0/arm-exe/include -I/home/genglei/common/ncurses-6.1/arm-exe/include" LIBS="-lssl -lcrypto -lreadline -lncurses"
```



# ncat

下载链接：https://nmap.org

依赖库：libpcap，openssl(包含libssl，libcrypto），libpcre

##openssl

编译如上。

## libpcre

下载链接：https://ftp.pcre.org/pub/pcre/

```
cd libpcap-1.8.1
mkdir arm-exe
./configure --prefix=/home/genglei/common/libpcap-1.8.1/arm-exe --host=arm-poky-linux-gnueabi  --disable-shared --enable-dbus=no
make && make install
```



## libpcap

下载链接：<http://www.tcpdump.org>

```
cd libpcap-1.9.0
mkdir arm-exe
imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi
./configure --prefix=/home/genglei/common/libpcap-1.9.0/arm-exe --disable-shared --enable-dbus=no
make && make install

注意：需要把dbus禁用掉，否则libpcap动态库会链接libdbus动态库，导致ncat如果用静态方式链接pcap静态库失败，只能链接pcap动态库。
```



## ncat

```
cd nmap-7.70

mkdir arm-exe

imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi

./configure --prefix=/home/genglei/common/nmap-7.70/arm-exe --host=arm-poky-linux-gnueabi --without-nmap-update --without-liblua --without-ndiff --without-zenmap --without-nping --with-libpcre=/home/genglei/common/pcre-8.42/arm-exe --without-libssh2 --with-libpcap=/home/genglei/common/libpcap-1.9.0/arm-exe/include --with-libpcap=/home/genglei/common/libpcap-1.9.0/arm-exe  --with-openssl=/home/genglei/common/openssl-1.1.0h
make && make install
```



# tcpflow

下载链接：http://downloads.digitalcorpora.org/downloads/tcpflow/

**tcpflow-1.5.0-alpha2**

依赖库： libpcap，openssl（libssl,libcrypto），libhttp_parser,libpython2.7（可选），libsqlite3（可选），libz

采用静态链接

```
./configure --prefix=/home/genglei/common/tcpflow-1.5.0-alpha2/armv7-exe --host=arm-poky-linux-gnueabi  --enable-cairo=false  CFLAGS="-I/home/genglei/common/zlib-1.2.8/arm-exe/include -I/home/genglei/common/libpcap-1.9.0/arm-exe/include -I/home/genglei/common -I/home/genglei/common/boost_1_66_0/arm-exe/include -I/home/genglei/common/http-parser-2.3/arm-exe -I/home/genglei/common/openssl-1.1.0h.bak/arm-exe/include" LDFLAGS="-L/home/genglei/common/zlib-1.2.8/arm-exe/lib -L/home/genglei/common/libpcap-1.9.0/arm-exe/lib -L/home/genglei/common/boost_1_66_0/arm-exe/lib -L/home/genglei/common/http-parser-2.3/arm-exe -L/home/genglei/common/openssl-1.1.0h.bak/arm-exe/lib" CXXFLAGS="-I/home/genglei/common/zlib-1.2.8/arm-exe/include -I/home/genglei/common/libpcap-1.9.0/arm-exe/include -I/home/genglei/common/boost_1_66_0/arm-exe/include -I/home/genglei/common -I/home/genglei/common/http-parser-2.3/arm-exe -I/home/genglei/common/openssl-1.1.0h.bak/arm-exe/include" LIBS=" -lssl -lcrypto -lpcap -lhttp_parser"
```



**重点记录交叉编译openssl的过程：**

```
Imx6平台：
imx6：source /opt/imx6/environment-setup-cortexa7hf-vfp-neon-poky-linux-gnueabi
注释导入的环境变量PATH部分内容，不能查找perl到交叉编译目录下，应该使用本机perl，不然执行下面config命令时会出错，config命令需要使用本机perl执行。

./config --cross-compile-prefix=arm-poky-linux-gnueabi- --prefix=/home/genglei/common/openssl-1.1.0h.bak/arm-exe --openssldir=/home/genglei/common/openssl-1.1.0h.bak/arm-exe no-asm
no-asm不为arm产生汇编指令。

unset CROSS_COMPILE
注释脚本里的CROSS_COMPILE
删除Makefile -m64编译选项

make && make install
```



### imx28 交叉编译tcpflow

编译tcpflow需要c++boost库支持。

c++ boost 下载网址：https://sourceforge.net/projects/boost/files/boost/1.66.0/

```
解压boost压缩包
进入目录执行./bootstrap.sh,此时生成bjam文件和project-config.jam文件
编辑project-config.jam文件，修改using gcc这行，改成如下形式：
if ! gcc in [ feature.values <toolset> ]
{
    using gcc : arm : arm-none-linux-gnueabi-g++ ; 
}
注意空格
export CC=arm-none-linux-gnueabi-gcc
export CXX=arm-none-linux-gnueabi-g++

执行./bjam --prefix=/home/genglei/common/boost_1_66_0/armv5-exe --without-python
之后生成的库在boost_1_66_0/stage/lib目录下，头文件在根目录

以下是编译完后的提示：
The Boost C++ Libraries were successfully built!

The following directory should be added to compiler include paths:

    /home/genglei/common/boost_1_66_0

The following directory should be added to linker library paths:

    /home/genglei/common/boost_1_66_0/stage/lib

```

```
./configure --prefix=/home/genglei/common/tcpflow-1.5.0-alpha2/armv5-exe --host=arm-none-linux-gnueabi --enable-cairo=false  CFLAGS="-I/home/genglei/common/zlib-1.2.8/armv5-exe/include -I/home/genglei/common/libpcap-1.9.0/armv5-exe/include -I/home/genglei/common/boost_1_66_0 -I/home/genglei/common/http-parser-2.3/armv5-exe -I/home/genglei/common/openssl-1.1.0h/include" LDFLAGS="-L/home/genglei/common/zlib-1.2.8/armv5-exe/lib -L/home/genglei/common/libpcap-1.9.0/armv5-exe/lib -L/home/genglei/common/boost_1_66_0/arm-exe/lib -L/home/genglei/common/http-parser-2.3/armv5-exe -L/home/genglei/common/openssl-1.1.0h/lib -L/home/genglei/common/boost_1_66_0/stage/lib"  CXXFLAGS="-I/home/genglei/common/zlib-1.2.8/armv5-exe/include -I/home/genglei/common/libpcap-1.9.0/armv5-exe/include -I/home/genglei/common/boost_1_66_0 -I/home/genglei/common/http-parser-2.3/armv5-exe -I/home/genglei/common/openssl-1.1.0h/include" LIBS="-lssl -lcrypto -lpcap -lhttp_parser"
```

