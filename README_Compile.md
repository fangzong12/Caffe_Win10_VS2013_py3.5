# Win10+CPU+Annaconda3+VS2015+Cmake3.11编译、安装Caffe，日志

- 本文博客目录https://blog.csdn.net/confusingbird/article/details/98056261
- 最新Annaconda3下载路径：https://www.anaconda.com/distribution/选取python3.7

![](D:\WorkDiary\Caffe配置\Annaconda3下载.jpg)

- VS2015下载路径：https://visualstudio.microsoft.com/zh-hans/vs/older-downloads/

- Cmake3.11下载路径：https://github.com/Kitware/CMake/releases/download/v3.11.2/cmake-3.11.2-win64-x64.zip

  

  #### 1. 下载Caffe-Windows地址在[BVLC/caffe](https://github.com/BVLC/caffe/tree/windows)

  ![1564633274375](D:\WorkDiary\Caffe配置\Caffe_Windows下载.png)

  

   我用下面这个链接，跳转不成功，于是只能自己编译了

![1564633385477](D:\WorkDiary\Caffe配置\Caffe_release_link.png)

#### 2. 安装Annaconda3、VS2015、Cmake3.11

- Annaconda3+tensorflow的安装教程https://www.cnblogs.com/HongjianChen/p/8385547.html，清晰明了

- VS2015一定要把VC++的内容全部包含安装
- 其他的网上教程很多，而且安装比较方便，我就不一一叙述了



#### 3. 配置Caffe

打开你下载的caffe文件夹，找到scripts文件夹中的build_win.cmd，修改如下：

![1564634437059](D:\WorkDiary\Caffe配置\Caffe_Script_build_win_cmd.png)

![1564634572454](D:\WorkDiary\Caffe配置\Caffe_Script_build_win_cmd2.png)

同时修改python2和python3在conda的路径，**即Anaconda安装的位置**，例如

![1564634654386](D:\WorkDiary\Caffe配置\Caffe_Script_build_win_cmd3.png)

#### 4. Cmake编译

打开cmake-gui.exe（在D:\cmake-3.11.0-win64-x64\bin里）设置source code和the binaries的目录。一个是caffe的根目录，一个是里面的build文件夹，如下：

![1564634845004](D:\WorkDiary\Caffe配置\Cmake_编译.png)

点击configure，选择Visual Studio 14 2015 Win64

可能会报错没有GPU和blas，解决方案：

   a.在Search窗口里输入cpu，在Ungrouped Entires中将CPU_ONLY选中

   b.在Search窗口里输入blas，在Ungrouped Entires中将BLAS选择为Open

#### 5. Cmake报错Could not find url for MSVC version = 1900 and Python version = 3.7

##### 错误原因：由于Caffe只提供两个版本（2.7和3.5）的python选择

##### 解决方案：

##### 1）下载python2.7的Annaconda2

##### 2）下载python3.5的Annaconda4.2.0

##### 3）在最新的Annaconda里面新建一个python3.5的环境



##### 前两个方案很简单，但是可能带来一些隐患，这里主要讲解选择方案三：

- 而咱们用的是Annaconda3，所以需要新建一个python3.5的环境，类似Tensorflow的配置https://www.anaconda.com/distribution/或者直接在Tensorflow里面配置python3.5，如下：

![1564634514733](D:\WorkDiary\Caffe配置\Annaconda3_python3.5.png)



再次Cmake，点击file->delete Cache，然后点击configure，选择Visual Studio 14 2015 Win64，还是原来的问题，后来我跟踪到一个代码里面:                                                                            Caffe/cmake/WindowsDownloadPrebuiltDependencies.cmake，错误提示在第40行，而错误发生在29行的地方，如下:

![1564638453134](D:\WorkDiary\Caffe配置\Caffe_cmake_WindowsDoloadPre.png)

分析原因是find_package(PythonInterp 3.5)没有找到python3.5，而是找到了Annaconda的根目录默认的python3.7，于是我查看对应的API，如下：

![1564638712754](D:\WorkDiary\Caffe配置\FindPthonInterp_API.png)

原来是为了配置这些属性，于是我直接复制，将Caffe/cmake/WindowsDownloadPrebuiltDependencies.cmake第29行修改为如下：

![1564638862256](D:\WorkDiary\Caffe配置\Caffe_cmake_WindowsDoloadPre2.png)

再次Cmake，点击file->delete Cache，然后点击configure，选择Visual Studio 14 2015 Win64，发现错误变了

#### 6. 错误.tar.bz2

这个就简单了，直接迅雷手动下载https://github.com/willyd/caffe-builder/releases/download/v1.1.0/libraries_v140_x64_py35_1.1.0.tar.bz2，完成后复制到C\user\caffe\ dependencies\download\ 下，并重命名为.tar.bz2

再次Cmake，点击file->delete Cache，然后点击configure，done，完成，然后点击Generate，生成成功。

#### 7. VS2015编译Caffe

进入build文件夹（D:\caffe-windows\build）找到Caffe.sln文件，双击打开，它就会在VS中打开

![1564639555362](D:\WorkDiary\Caffe配置\VS2015_Caffe_0.png)

设置工程解决方案，设置为Release x64，并把ALL_BUILD设置成启动项

![1564639670557](D:\WorkDiary\Caffe配置\VS2015_Caffe_2.png)

重新生成项目2两次，第一次37成功1失败，第二次38成功

![1564639738672](D:\WorkDiary\Caffe配置\VS2015_Caffe_3.png)

![1564639768430](D:\WorkDiary\Caffe配置\VS2015_Caffe_4.png)

这样就完全编译成功了

#### 8. 最后的配置

将caffe目录下的python/caffe整个文件夹，复制到D:\Anaconda\Anaconda\envs\tensorflow3.5\Lib\site-packages

在实际使用过程中，你可能还用到一些自己常用的第三方库，比如numpy、ninja、scipy、protobuf、scikit-image等等，百度一下，安装很简单，建议使用：pip install numpy

#### 9. 参考文献

https://blog.csdn.net/baidu_36669549/article/details/79793418

https://blog.csdn.net/liliji8846/article/details/88552947

https://blog.csdn.net/matt45m/article/details/89330058