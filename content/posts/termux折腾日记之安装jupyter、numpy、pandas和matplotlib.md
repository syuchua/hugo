---
title: termux折腾日记之安装jupyter、numpy、pandas和matplotlib
categories:
  - blog
tags:
  - Termux
  - jupyter
  - numpy
  - python
  - pandas
  - matplotlib
date: 2024-05-02T22:34:29+08:00
---
前几天因为课程需要折腾了一下遗传算法，因为担心老师课上提问，带电脑又不方便，变突发奇想程序能不能在手机上运行，毕竟手机上也是有终端环境——termux的。但是termux毕竟只是终端，无法生成函数图像。你说x11？算了吧，还不如直接上电脑呢。

于是想到了jupyter——一款支持图像输出的在线强大工具，只需要浏览器就能运行。话不多说，直接上手安装:
`pip install jupyter`
毫无疑问报错了，报的错还看不懂，说是缺了依赖，那我用pkg安装依赖呢？还报错，一会儿说是这个依赖还需要某某依赖，一会又是早轮子报错……没办法，上网找教程吧。

现实是，网上安装jupyter的教程是不少，可大都是PC端的，termux少得可怜，即便有，也大都是三四年前的，不同的人甚至有不同的解决办法，可这些方法都试过一遍后，也都无一例外的失败了。但功夫不负有心人，最后还是在CSDN上找到一篇接近成功的方法，这里贴出原文链接，以供参考:
http://t.csdnimg.cn/r7K6y

但这篇博客终究还是有一些瑕疵，索性问题都不是很大，只是一些版本兼容性问题，比方说在安装numpy，matplotlib，pandas，jupyter等的时候因为不同的版本之间不一定兼容，动不动会出现一大堆的报错，解决方法也简单，就是把pip换成pkg安装，因为pip安装往往需要经过编译，而编译又需要某些特定的工具和库，大都是termux环境中没有的，pkg安装则是直接安装pkg中预编译好的二进制文件，早已跳过了通过工具链编译这一步。举个简单的例子，比方说我要安装numpy和pandas，就不必使用
```
pip install numpy pandas
```

或者
```
MATHLIB="m" pip install pandas
MATHLIB="m" pip install numpy
```

而是换成

```
pkg install -y python-numpy python-pandas
```

包括在安装jupyter时经常编译出错的pyzmq，也可以这样安装，后面跟上`==25.1.1`指的是版本，因为pkg和pip总是默认安装最新的，但测试发现目前最新的26.0.2版本(2024年4月底)有兼容问题，遂换成25.1.1版本

```
pkg install -y libzmq libcrypt pkg-config  # 先安装一些必要的依赖库

# 再通过pkg安装pyzmq

pkg install -y python-pyzmq==25.1.1

# 或者
pip install pyzmq==25.1.1
```

安装jupyter中还有一个经常出现的依赖报错是maturin，这是一个rust里的文件，同样是因为一些版本兼容问题，在Python3.11的环境下是无法正常编译安装的，这个时候就只能先安装rust环境，再用cargo安装了:

```
pkg install rust  #安装rust
pkg install binutils-is-llvm
cargo install maturin
pkg install -y libzmq libcrypt pkg-config
pip install clang fftw make freetype # 一些编译有关依赖
pip install ipython  # jupyter依赖ipython
```

之后再安装jupyter应该就不会报错了

```
pip install jupyter
```

安装matplotlib的过程也是常常因为一些依赖报错，但比起jupyter还是简单不少的

```
pkg install -y libjpeg-turbo libtiff
pkg install python-pillow
pkg install -y libpng
pkg install matplotlib
```

总的来说，在termux里折腾jupyter说难不难，说简单也不简单吧，毕竟也是花了我几个小时的时间，最后，我把上面的过程都写成了一个脚本，方便大家自动化安装，地址如下:
https://github.com/syuchua/termux-jupyter

如果是直接使用的话可以使用wget下载到本地:
```
wget https://raw.githubusercontent.com/syuchua/termux-jupyter/master/install.sh -O install.sh
chmod +x install.sh
./install.sh
```
![](https://cdn.jsdelivr.net/gh/mazhijia/jsdeliver@main/img/termux-jupyter.jpg)

最后，如果觉得本教程或者这个脚本还可以的话，麻烦给我一个star，谢谢！