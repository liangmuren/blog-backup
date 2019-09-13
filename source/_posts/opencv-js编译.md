---
title: opencv.js编译
tags: 'JavaScript, opencv'
date: 2019-04-14 20:23:57
---


最近老师想把之前的做过的一些AR小游戏弄到手机或者微信浏览器中，于是让我看一看 jsfeat, Opencv.js 以及Tensorflow.js。网上的资料大多都不是特别新，稍作参考，很不容易地将 Opencv.js 基础部分弄好了。我也整理了点东西，作为记录，也供之后的人参考。

## 目录

* [安装Emscripten环境](#安装Emscripten环境)
* [编译opencv.js](编译opencv.js)
* [网页加载](网页加载)



## 安装Emscripten

opencv 是一个用 C++ 编写的项目，而 opencv.js 则是在浏览器环境下执行的JS代码，这个转化过程需要使用 Emscripten 将代码转换成 WebAssembly 格式的二进制类汇编格式。所以我们首先需要安装 Emscripten。这个步骤需要从 Emscripten 源码进行编译。

安装需要有 git, cmake, C++ 编译环境（如 Unix 系统上的 GCC 或 Windows 上的 Visual Studio Runtime）以及 Python 2.7.x 。虽然 Windows 环境下编译 Opencv.js 是可行的，但在实际过程中会有各种依赖和环境变量的问题，所以建议使用 Unix 系统进行之后的操作。网上使用 Windows 编译的大多数也是使用 [WSL](https://en.wikipedia.org/wiki/Windows_Subsystem_for_Linux) (Windows Subsystem for Linux) 来安装的。

安装编译命令如下：

```shell
git clone https://github.com/juj/emsdk.git
cd emsdk

# 在 Linux 或者 Mac OS X 上
./emsdk install --build=Release sdk-incoming-64bit binaryen-master-64bit
./emsdk activate --global --build=Release sdk-incoming-64bit binaryen-master-64bit

# 在 Windows 上
emsdk install --build=Release sdk-incoming-64bit binaryen-master-64bit
emsdk activate --global --build=Release sdk-incoming-64bit binaryen-master-64bit

# 注意：Windows 版本的 Visual Studio 2017 已经被支持，但需要在 emsdk install 需要追加 --vs2017 参数
```

--buildd=Release 表示选择安装 Release 版的LLVM编译器类型，sdk-incoming-64bit binaryen-master-64bit 为指定安装的SDK类型。此外可以加上 -j 来指定编译过程中使用的 CPU 核数。如果要看更多安装命令的参数及说明，可以通过  `./emsdk --help` 来查看。

之后程序会依次将 sdk 和 tool 的源文件 clone 到本地然后用cmake编译。如果中途因为断网或者编译错误导致安装中断，在排除问题后脚本会从安装中断处继续。

在编译完成之后，需要将相关程序加入环境变量以便在命令行中使用。也可以每次需要进行编译之前执行脚本：

```shell
# on Linux or Mac OS X
source ./emsdk_env.sh

# on Windows
emsdk_env.bat
```

## 编译opencv.js

在 github 上下下载 Release 版的 opencv 源码。

源码可以在 https://github.com/opencv/opencv/releases 找到。比如：

```shell
wget https://github.com/opencv/opencv/archive/4.1.0.zip
```

解压进入目录。

```shell
unzip 4.1.0.zip
cd opencv-4.1.0
```

在 ./platforms/js/ 目录下有一个 pyhon脚本 build_js.py， 运行

```shell
python ./platforms/js/build_js.py --help
```

可以看到一些说明

![1555243053700](C:\Users\flord\AppData\Roaming\Typora\typora-user-images\1555243053700.png)

可以通过参数来选择编译的配置和产生的内容，包括.js文件，测试，文档等。

运行编译wasm后：

```shell
python ./platforms/js/build_js.py build_dir --build_wasm
```

编译的结果会出现在 `build_dir/bin` 文件夹中。

在 opencv 3.x  的版本下，会得到  `opencv.js opencv_js.js opencv_js.wasm` 三个文件，这也是网上各种教程中常使用的版本。 `opencv.js` 是引用的 js 文件， `opencv_js.js` 是中间生成的 asm.js ，`opencv_js.wasm` 是 WebAssembly 格式的二进制文件。

而在 opendcv 4.x 的版本下，只会有  `opencv.js opencv_js.js `  这两个文件，但其实 WebAssembly 的内容已经编译成功，但已经嵌入.js文件中，在.js文件中有：

```javascript
var wasmBinaryFile="data:application/octet-stream;base64,....'
```



## 网页加载

之后我们就可以在页面中引用 `opencv.js` 了。使用 4.x 版本的话，只需要在 HTML 文件中加入:

```html
<script async src="opencv/opencv.js"></script>
```

该 JS 文件载入后会自动运行，并异步初始化 cv ，在初始化之后调用`cv['onRuntimeInitialized']`。所以可以通过

```javascript
cv['onRuntimeInitialized'] = () =>{
	// 你的代码
}
```

来调用 cv 这个 Object 以及上面的各种方法。