---
title: Kaldi(1)
date: 2018-11-13 15:44:55
tags: Kaldi
---

> Kaldi is a toolkit for speech recognition, intended for use by speech 
> recognition researchers and professionals. Find the code repository at <http://github.com/kaldi-asr/kaldi>.

#### 下载

> git clone https://github.com/kaldi-asr/kaldi.git kaldi-trunk --origin golden

#### 安装

虽然Kaldi可以在Windows下安装，但实际操作起来要安装大量依赖软件，十分麻烦，出错也缺少资料查询参考。在自己电脑的Windows环境下安装失败后，我选择在实验室机器的虚拟机上安装Kaldi。操作系统环境是Ubuntu18.04LTS。

使用git下载之后，我们得到了一个kaldi-trunk文件夹。里面结构如下。

kaldi-trunk
├─egs       Kaldi示例的脚本，使用户可以
├─misc      与kaldi本身功能无关的一些额外工具和用品，包括模型转换脚本、kaldi logo和相关论文
├─scripts   
├─src       Kaldi源代码
├─tools     组件和外部工具
├─windows   使用Windows运行Kaldi的工具