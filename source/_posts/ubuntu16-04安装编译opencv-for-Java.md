---
title: Ubuntu16.04安装编译OpenCV for Java
keywords: 'Ubuntu,OpenCV，编译，Java'
description: Ubuntu16.04安装编译OpenCV for Java
abbrlink: 58e44ad3
date: 2020-04-04 14:52:30
categories:
tags:
---

## 一、安装cmake

下载安装包：  https://github.com/Kitware/CMake/releases?after=v3.10.0-rc2  用的版本是3.9.2

解压缩

配置环境变量

~~~shell
export PATH=$PATH:/home/master/install/cmake-3.9.2-Linux-x86_64/bin
export CMAKE_ROOT=/home/master/install/cmake-3.9.2-Linux-x86_64
~~~

安装完毕进行测试：

`cmake --version`

参考：[CMake Error: Could not find CMAKE_ROOT?]( https://askubuntu.com/questions/1014670/cmake-error-could-not-find-cmake-root )

## 二、Ubuntu16.04 LTS安装OpenCV3.4.9及OpenCV Contrib for Java

### 1.国内服务器更换阿里云源

~~~
sudo cp /etc/apt/sources.list /etc/apt/sources.list.old
sudo vim /etc/apt/sources.list
~~~

阿里云源地址：[Ubuntu 镜像](https://developer.aliyun.com/mirror/ubuntu?spm=a2c6h.13651102.0.0.3e221b11d3eimD)

~~~shell
sudo apt-get update
sudo apt-get upgrade
~~~

添加github相关hosts

CMakeDownloadLog.txt

~~~shell
#try 1
#   Trying 151.101.228.133...
# TCP_NODELAY set
# connect to 151.101.228.133 port 443 failed: Connection reset by peer
# Failed to connect to raw.githubusercontent.com port 443: Connection reset by peer
# Closing connection 0
~~~

去[ipaddress](https://www.ipaddress.com/)上面查询`github.com`和`raw.githubusercontent.com`相关的ip，然后添加到/etc/hosts文件中：

~~~shell
# GitHub Start
140.82.113.3 	github.com
199.232.28.133 	raw.githubusercontent.com
185.199.108.153 assets-cdn.github.com
199.232.5.194 	github.global.ssl.fastly.net
140.82.112.10 	codeload.github.com
199.232.68.133 	gist.githubusercontent.com
199.232.28.133 	cloud.githubusercontent.com
199.232.28.133 	camo.githubusercontent.com
199.232.68.133 	avatars0.githubusercontent.com
199.232.68.133 	avatars1.githubusercontent.com
199.232.68.133 	avatars2.githubusercontent.com
199.232.68.133 	avatars3.githubusercontent.com
199.232.68.133 	avatars4.githubusercontent.com
199.232.68.133 	avatars5.githubusercontent.com
199.232.68.133 	avatars6.githubusercontent.com
199.232.68.133 	avatars7.githubusercontent.com
199.232.68.133 	avatars8.githubusercontent.com
140.82.113.6  	api.github.com
# GitHub End
~~~

同时安装证书：[解决“正在连接 raw.githubusercontent.com|151.101.228.133|:443... 失败：拒绝连接”的方法](https://blog.csdn.net/weixin_43201726/article/details/82967342)

[解决Github国内访问出现的问题](http://rovo98.coding.me/posts/7e3029b3/)

### 2.安装软件相关的依赖

~~~shell
[compiler] sudo apt-get install build-essential
[required] sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
[optional] sudo apt-get install python-dev python-numpy libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev
sudo apt-get install ant #Java环境

sudo apt-get install build-essential libgtk2.0-dev libjpeg-dev libtiff5-dev libjasper-dev libopenexr-dev python-dev python-numpy libtbb-dev libeigen3-dev yasm libfaac-dev libopencore-amrnb-dev libopencore-amrwb-dev libtheora-dev libvorbis-dev libxvidcore-dev libx264-dev sphinx-common texlive-latex-extra libv4l-dev libdc1394-22-dev libavcodec-dev libavformat-dev libswscale-dev  #ant cmake和openjdk以单独安装，所以不用在这里面再重新安装一次。
python-tk libvtk5-dev libvtk5-qt4-dev libqt4-dev libqt4-opengl-dev 未安装
~~~

注意：<font color="red" style="font-weight:bold">依赖谨慎安装，不是安装越多越好！按照自己需要的安装就行。</font>

安装OpenCV源文档：[Installation in Linux](https://docs.opencv.org/3.4.9/d7/d9f/tutorial_linux_install.html)

“在生成OpenCV的Makefile之前，cmake工具会检查当前系统中是否已经配置好了Java环境，以决定是否会生成Java开发相应的包。因此，我们首先要确认当前系统已经配置好了Java的开发环境，这一部分内容不属于本文的重点，请参看其他文章。”

**问题：**

~~~
liblzma-dev : 依赖: liblzma5 (= 5.1.1alpha+20120614-2ubuntu2) 但是 5.2.2-1.3 正要被安装
~~~

解决：[ubuntu安装软件时:有未能满足的依赖关系](https://blog.csdn.net/qq_36396104/article/details/79748458)，使用aptitude，解决问题。

**使用知识：**

~~~shell
# 查看软件是否安装
dpkg -l |grep -i "软件名"
# 安装某软件包
sudo dpkg -i package.deb
# 卸载软件
sudo dpkg -r 软件名
sudo apt-get autoremove 软件名

# 软件降级：
sudo apt-get install <package-name>=<package-version-number> OR
sudo apt-get -t=<target release> install <package-name>
# 或者
sudo apt-get install aptitude
sudo aptitude install 包名=包的版本号  
~~~

~~~
-- The imported target "vtkInfovisJava" references the file
   "/usr/lib/jni/libvtkInfovisJava.so.5.10.1"
but this file does not exist.  Possible reasons include:
* The file was deleted, renamed, or moved to another location.
* An install or uninstall procedure did not complete successfully.
* The installation package was faulty and contained
   "/usr/lib/vtk-5.10/VTKTargets.cmake"
but not all the files it references.
...
~~~

### 3.下载源码并创建编译文件夹

创建一个文件夹用来存储编译产生的文件

下载地址：[opencv源码](https://github.com/opencv/opencv/releases)、[opencv contrib源码](https://github.com/opencv/opencv_contrib/releases)

**注意：**请确保opencv和opencv contrib的版本是一致的。

~~~shell
cd ~/opencv
mkdir build
cd build
~~~

### 4.使用CMake编译OpenCV

`cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D BUILD_TESTS=OFF ..`

如果报错，去掉-D后面的空格，即：

`cmake -DCMAKE_BUILD_TYPE=Release -DCMAKE_INSTALL_PREFIX=/usr/local -DBUILD_TESTS=OFF ..`

~~~shell
cmake -DOPENCV_ENABLE_NONFREE=ON -DCMAKE_BUILD_TYPE=Release -DOPENCV_GENERATE_PKGCONFIG=ON -DENABLE_PRECOMPILED_HEADERS=OFF -DBUILD_TIFF=ON -DOPENCV_EXTRA_MODULES_PATH=<path/to/opencv_contrib/modules/> -DCMAKE_INSTALL_PREFIX=/usr/local ..

cmake -D CMAKE_BUILD_TYPE=Release -D CMAKE_INSTALL_PREFIX=/usr/local -D BUILD_SHARED_LIBS=OFF -D BUILD_EXAMPLES=OFF -D BUILD_TESTS=OFF -D BUILD_PERF_TESTS=OFF -D OPENCV_EXTRA_MODULES_PATH=/home/edward/Downloads/opencv_contrib-master/modules/ -D BUILD_opencv_text=OFF ..

cmake \
-DOPENCV_ENABLE_NONFREE=ON \
-DCMAKE_BUILD_TYPE=Release \
-DOPENCV_GENERATE_PKGCONFIG=ON \
-DENABLE_PRECOMPILED_HEADERS=OFF \
-DBUILD_TIFF=ON \
-DBUILD_EXAMPLES=OFF \
-DBUILD_TESTS=OFF \
-DOPENCV_EXTRA_MODULES_PATH=/home/liwen/install/opencv_contrib-3.4.9/modules \
-DCMAKE_INSTALL_PREFIX=/usr/local \
..
~~~

https://raw.githubusercontent.com%2Fopencv%2Fopencv_3rdparty%2F32e315a5b106a7b89dbed51c28f8120a48b368b4%2Fippicv%2Fippicv_2019_lnx_intel64_general_20180723.tgz

opencv-3.4.9/3rdparty/ippicv/ippicv.cmake

~~~
"${OPENCV_IPPICV_URL}"
                 "$ENV{OPENCV_IPPICV_URL}"
                 "https://raw.githubusercontent.com/opencv/opencv_3rdparty/${IPPICV_COMMIT}/ippicv/"
~~~

修改为本地路径：`file:///home/liwen/package/opencvDownLoads/`

opencv_contrib-3.4.9/modules/face/CMakeLists.txt

https://blog.csdn.net/qq_34806812/article/details/82501999

**注意：确保To be built有java**

![微信截图_20200304165015.png](http://ww1.sinaimg.cn/large/75a4a8eegy1gci35zh511j21gl07f74o.jpg)

![微信截图_20200304165034.png](http://ww1.sinaimg.cn/large/75a4a8eegy1gci36la42ij21gh06vq32.jpg)

### 5.编译

~~~shell
sudo make -j7  #使用7个线程编译
~~~

但是使用自己的机器编译时，使用多线程常出错，错误信息：

~~~shell
[ 42%] Building CXX object modules/core/CMakeFiles/opencv_core.dir/mathfuncs_core.avx.cpp.o
[ 42%] Linking CXX shared library ../../lib/libopencv_core.so
/usr/bin/ld: CMakeFiles/opencv_core.dir/src/matmul.dispatch.cpp.o: relocation R_X86_64_PC32 against undefined symbol `void cv::cpu_baseline::MulTransposedR<unsigned char, float>(cv::Mat const&, cv::Mat const&, cv::Mat const&, double)' can not be used when making a shared object; recompile with -fPIC
/usr/bin/ld: final link failed: Bad value
collect2: ld returned 1 exit status
make[2]: *** [lib/libopencv_core.so.3.4.9] Error 1
make[1]: *** [modules/core/CMakeFiles/opencv_core.dir/all] Error 2
make: *** [all] Error 2
~~~

所以，选择单线程编译：

~~~shell
sudo make
~~~

**问题**

~~~shell
[ 97%] Generating opencv-349.jar
Unable to locate tools.jar. Expected to find it in /usr/lib/jvm/java-8-openjdk-amd64/lib/tools.jar
Buildfile: /home/hadoop/install/opencv-3.4.9/build/modules/java/jar/opencv/build.xml

jar:
Target 'jar' failed with message 'Unable to find a javac compiler;
com.sun.tools.javac.Main is not on the classpath.
Perhaps JAVA_HOME does not point to the JDK.
It is currently set to "/usr/lib/jvm/java-8-openjdk-amd64/jre"'.

BUILD FAILED
/home/hadoop/install/opencv-3.4.9/build/modules/java/jar/opencv/build.xml:14: Unable to find a javac compiler;
com.sun.tools.javac.Main is not on the classpath.
Perhaps JAVA_HOME does not point to the JDK.
It is currently set to "/usr/lib/jvm/java-8-openjdk-amd64/jre"

Total time: 0 seconds
modules/java/jar/CMakeFiles/opencv_java_jar.dir/build.make:61: recipe for target 'CMakeFiles/dephelper/opencv_java_jar' failed
make[2]: *** [CMakeFiles/dephelper/opencv_java_jar] Error 1
CMakeFiles/Makefile2:3258: recipe for target 'modules/java/jar/CMakeFiles/opencv_java_jar.dir/all' failed
make[1]: *** [modules/java/jar/CMakeFiles/opencv_java_jar.dir/all] Error 2
Makefile:162: recipe for target 'all' failed
make: *** [all] Error 2
~~~

原因：自己安装了jdk，另外系统里面又安装了open-jdk，导致环境冲突。

解决：卸载open-jdk，同时添加CLASSPATH

~~~shell
sudo apt-get remove openjdk*
~~~

~~~shell
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
~~~

或者安装openjdk和ant

~~~shell
sudo apt-get install openjdk-8-jdk
sudo apt-get install ant
# 卸载
sudo apt-get remove openjdk*
~~~

如果有： **ImportError: /usr/local/lib/libopencv_freetype.so.3.2: undefined symbol: hb_shape**

~~~shell
sed -i 's/${freetype2_LIBRARIES} ${harfbuzz_LIBRARIES}/${FREETYPE_LIBRARIES} ${HARFBUZZ_LIBRARIES}/g' ../opencv_contrib-3.2.0/modules/freetype/CMakeLists.txt
~~~

### 6.安装库，从构建目录执行以下命令

~~~shell
sudo make install
~~~

Java开发相关的动态链接库文件和jar包位于目录：

```shell
/usr/local/share/OpenCV/java/
```

参考：

[1.**Installation in Linux**](https://docs.opencv.org/3.4.9/d7/d9f/tutorial_linux_install.html)

[2.Getting started with OpenCV for Java on Ubuntu](https://advancedweb.hu/getting-started-with-opencv-for-java-on-ubuntu/)

[3.Ubuntu 16.04配置OpenCV 3.1.0 for Java](https://www.cnblogs.com/xiaomanon/p/5490281.html)

[4.ubuntu16.04安装opencv3.4.1教程](https://blog.csdn.net/cocoaqin/article/details/78163171)

[5.在 Ubuntu系统下安装 OpenCV 全过程](https://www.voorp.com/a/在Ubuntu系统下安装OpenCV全过程潇洒过路客的博客CSDN博客)

[6.linux下安装并使用java开发opencv的配置](http://x-wei.github.io/linux下安装并使用java开发opencv的配置.html)

[7.linux下安装opencv并生成opencv-java，即linux下用java调用opencv]()

[8.Ubuntu下源码安装Opencv完全指南](https://oldpan.me/archives/ubuntu-install-opencv-from-source)

[9.如何在Linux下编译安装OpenCV](https://www.linuxprobe.com/linux-install-opencv.html)

## 三、卸载OpenCV

1.查看已安装的opencv版本

`pkg-config --modversion opencv`

2.卸载

首先要找到当初安装opencv的build目录，进入该build目录执行卸载操作

~~~shell
sudo make uninstall
cd  ..
rm -r build
~~~

如果找不见该build目录，可以重新建立build目录安装对应版本，然后再执行上边卸载步骤。然后清理/usr中所有opencv相关项

~~~
sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv*
cd /usr
find . -name "*opencv*" | xargs sudo rm -rf
~~~

eclipse安装

- 首先输入指令： **cd /usr/share/applications**
- 然后输入指令： **sudo vim eclipse.desktop**

~~~shell
[Desktop Entry]
Encoding=UTF-8
Name=Eclipse
Comment=Eclipse
Exec=/home/liwen/install/eclipse/eclipse
Icon=/home/liwen/install/eclipse/icon.xpm
Terminal=false
StartupNotify=true
Type=Application
Categories=Application;Development;
~~~

**其中“Exec=”后面为eclipse安装目录下的eclipse程序的位置路径，“Icon=”后面为eclipse安装目录下的图标图片的路径**

将eclipse变为可执行文件

**sudo chmod u+x eclipse.desktop**