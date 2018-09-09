# arm cortex-a7 linux perf嵌入式编译

**linux内核版本4.1.15**



## 1. 依赖库准备

| 特性特性           | 库                                       | 描述                                                         |
| :----------------- | :--------------------------------------- | :----------------------------------------------------------- |
| dwarf              | Debugging With Attributed Record Formats | GNU标准调试格式标准，目前已经发行到5.0版本，此库集成在elfutils工具中。下载网址：https://sourceware.org/elfutils/ftp/。 |
| glibc              | 标准库                                   | gnu标准C库，下载网址：https://www.gnu.org/software/libc/。   |
| libbfd             | Binary file descriptor library           | binutils工具集提供的一个库，用于获取elf可执行文件的section（节）以及symbol（符号）信息。下载网址：https://www.gnu.org/software/binutils/。 |
| libelf             | Executable and Linking Format library    | 用于解析elf二进制程序，此库集成在elfutils工具中，下载网址：https://sourceware.org/elfutils/ftp/。 |
| libslang           | libslang                                 | S-Lang是一个多平台程序员库，旨在允许开发人员创建强大的多平台软件。 它提供了交互式应用程序所需的功能，如显示/屏幕管理，键盘输入，键盘映射等等。下载网址：http://www.jedsoft.org/slang/。 |
| libunwind          | 函数栈回溯库                             | The libunwind project开源项目，此库集成在libunwind中，下载网址：http://www.nongnu.org |
| libdw-dwarf-unwind | 函数栈回溯库                             | The libunwind project开源项目，此库集成在libunwind中，下载网址：http://www.nongnu.org |
| zlib               | zlib                                     | 压缩库                                                       |
| lzma               | lzma                                     | Lempel-Ziv-Markov chain-Algorithm的缩写），是一个Deflate和LZ77[算法](https://baike.baidu.com/item/%E7%AE%97%E6%B3%95)改良和优化后的压缩算法。 |

------

## 2. 依赖库编译

### 2.1 elfutils

下载elfutils-0.170版本，交叉编译：

```
1. cd ～/common/elfutils-0.170
2. mkdir arm-exe build
3. cd build
4. ../configure --prefix=~/common/elfutils-0.170/arm-exe --host=arm-poky-linux-gnueabi
5. make && make install

生成libelf.so(libelf),libdw.so(dwarf)，将对应的库以及头文件copy到交叉编译sysroot下，为了避免影响交叉编译标准库的文件目录，在sysroot下创建usr/local/lib & usr/local/include目录，然后将库和头文件复制到对应目录下, 以便编译perf时可以detect到对应特性。
比如：sysroot=/opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi
mkdir -p /opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi/usr/local/lib
mkdir -p /opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi/usr/local/include
cd ~/common/elfutils-0.170/arm-exe
cp -rf lib/* /opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi/usr/local/lib
cp -rf include/* /opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi/usr/local/include

```

------

###2.2 binutils 

下载binutils-2.30，交叉编译，方法如上，推荐编译GNU项目时在源码目录创建build目录进行编译， ../configure option，生成libbfd.a静态库。

------

###2.3 libunwind

下载libunwind-1.2.1，交叉编译，方法如上。

------

### 2.4 libslang

下载slang-2.3.2，交叉编译，此库不支持build目录单独编译：

```
1. ~/common/slang-2.3.2
2. mkdir arm-exe
3. ./configure --prefix=/home/genglei/common/slang-2.3.2/arm-exe --host=arm-poky-linux-gnueabi --with-pcre=no
4. make && make install

如上，将库copy到sysroot交叉编译根目录下。
```

------

### 2.5 其他库类似

------

## 3. perf编译

更改Makefile，以便可以交叉编译，编译perf时有的Makefile直接把CC变量强制赋值为arm-poky-linux-gnueabi-gcc，而不带—sysroot选项，导致交叉编译时不能找到根目录，从而引起编译错误，比如头文件找不到或者feature检测失败等等，将此类makfile找出来并将CC变量赋值语句注释掉，使用shell环境变量定义的CC，环境变量CC为：

```
CC=arm-poky-linux-gnueabi-gcc -march=armv7-a -mfloat-abi=hard -mfpu=neon -mtune=cortex-a7 --sysroot=/opt/imx6/sysroots/cortexa7hf-vfp-neon-poky-linux-gnueabi
```

linux-4-1-15/tools/lib/api/Makefile 注释CC，使用环境变量定义的CC，保证可以使用--sysroot选项找到交叉编译根目录

linux-4-1-15/tools/perf/Makefile  注释CC，使用环境变量定义的CC，保证可以使用--sysroot选项找到交叉编译根目录

linux-4-1-15/tools/build/feature/Makefile 注释CC，使用环境变量定义的CC，保证可以使用--sysroot选项找到交叉编译根目录

```
#CC := $(CROSS_COMPILE)gcc -MD

```

linux-4-1-15/tools/perf/Makefile.perf

```
V=1 #输出详细的编译过程
VF=1 #输出详细的feature
# Define V to have a more verbose compile.
#
# Define VF to have a more verbose feature check output.

```



make 