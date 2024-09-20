# 创建环境

## 安装工具

``` c
$ sudo apt-get install autoconf automake autotools-dev curl python3 python3-pip libmpc-dev libmpfr-dev libgmp-dev gawk build-essential bison flex texinfo gperf libtool patchutils bc zlib1g-dev libexpat-dev ninja-build git cmake libglib2.0-dev libslirp-dev
```

## 下载仓库

使用gitee打包好的riscv 仓库

        
``` c
https://gitee.com/k_riscv/riscv-gnu-toolchain.git
```

git 之后:

中端执行

``` sh
echo "start"

cd binutils__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr binutils && mv binutils__delete_this_suffix binutils

cd dejagnu__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr dejagnu && mv dejagnu__delete_this_suffix dejagnu

cd gcc__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr gcc && mv gcc__delete_this_suffix gcc

cd gdb__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr gdb && mv gdb__delete_this_suffix gdb

cd glibc__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr glibc && mv glibc__delete_this_suffix glibc

cd musl__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr musl && mv musl__delete_this_suffix musl

cd newlib__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr newlib && mv newlib__delete_this_suffix newlib

cd pk__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr pk && mv pk__delete_this_suffix pv

cd qemu__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr qemu && mv qemu__delete_this_suffix qemu

cd spike__delete_this_suffix && touch git && mv git .git && cd ..

rm -fr spike && mv spike__delete_this_suffix spike

echo "done"
```


## 配置环境 && make

接下来执行
1. 嵌入式平台(没有操作系统)

``` c
./configure --prefix=/opt/riscv
make
```

2. linux(带才做系统的)

``` c
./configure --prefix=/opt/riscv
make linux
```

3. 都装

``` c
./configure --prefix=/opt/riscv --with-arch=rv32gc --with-abi=ilp32d
make linux
```


## 测试环境 

编译测试 

``` c
riscv64-unknown-elf-gcc hello.c 
```

执行结果

``` bash
fly@thinkpad:/mnt/e/CS_RISCV/riscv-operating-system-mooc/test$ riscv64-unknown-elf-gcc hello.c 
fly@thinkpad:/mnt/e/CS_RISCV/riscv-operating-system-mooc/test$ readelf -h a.out
ELF Header:
  Magic:   7f 45 4c 46 02 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF64
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           RISC-V
  Version:                           0x1
  Entry point address:               0x100c4
  Start of program headers:          64 (bytes into file)
  Start of section headers:          19824 (bytes into file)
  Flags:                             0x5, RVC, double-float ABI
  Size of this header:               64 (bytes)
  Size of program headers:           56 (bytes)
  Number of program headers:         2
  Size of section headers:           64 (bytes)
  Number of section headers:         15
  Section header string table index: 14
```


## qemu测试

``` c
qemu-riscv64 a.out

> hello world
```


**循序渐进，学习开发一个 RISC-V 上的操作系统**

<!-- TOC -->

- [创建环境](#创建环境)
  - [安装工具](#安装工具)
  - [下载仓库](#下载仓库)
  - [配置环境 \&\& make](#配置环境--make)
  - [测试环境](#测试环境)
  - [qemu测试](#qemu测试)
- [1. 简介](#1-简介)
- [2. 运行环境](#2-运行环境)
- [3. 构建和使用说明](#3-构建和使用说明)
- [4. 有关 RVOS 的移植](#4-有关-rvos-的移植)
- [5. 参考文献](#5-参考文献)

<!-- /TOC -->

# 1. 简介

本课程用于教学演示如何从零开始为 RISC-V 平台编写一个简单的操作系统内核。采用 BSD 2-Clause 许可证发布（具体请阅读本仓库根目录下的 [LICENSE 文件](./LICENSE)）。

本课程自 2021 年春季首次发布后的课程内容勘误表为本仓库根目录下的 `errata.pdf` 文件，在学习之前请先自行阅读。

如果您有任何的问题或者发现任何可疑的 bug，请通过 [Gitee 的 issue 跟踪系统](https://gitee.com/unicornx/riscv-operating-system-mooc/issues) 给我们提出报告，我们将第一时间检查并在系统中回复您。

**本课程的配套教学视频在线播放地址**: <https://www.bilibili.com/video/BV1Q5411w7z5>

欢迎加入本课程的 **学习群**，边学习边讨论：

- **QQ 学习群**

  **“课程群 2”**, 群号: **799027025**，或者扫码加入，见下：

  ![](./qq-group-2.png)

  ***注意！！！：“课程群 1” 已满，请大家加入 “课程群 2”。***

- **微信学习群**

  请添加微信 fangzhang1024 （标注 【汪辰老师】）加入。

# 2. 运行环境

推荐使用 Ubuntu 20.04，Ubuntu 20.04 是目前最新的 Ubuntu 长期稳定发行版，在这个环境下安装运行环境也最简单。

有些同学反映希望仍然使用Ubuntu 18.04，因为 Ubunt 18.04 的官方源还不支持 RISC-V 的开发工具包，所以搭建环境稍微复杂一些，我写了一个简单的说明文档，感兴趣的同学可以看 [这里](./howto-run-with-ubuntu1804_zh.md)。

所有演示代码在以下环境下验证通过，请仔细核对你的 Ubuntu 版本和内核版本与以下信息是否一致。

```
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.3 LTS
Release:	20.04
Codename:	focal

$ uname -r
5.11.0-27-generic
```

目前在 Ubuntu 20.04 环境下我们可以直接使用官方提供的 GNU工具链和 QEMU 模拟器，执行如下命令在线安装即可开始试验：

```
$ sudo apt update
$ sudo apt install build-essential gcc make perl dkms git gcc-riscv64-unknown-elf gdb-multiarch qemu-system-misc
```

# 3. 构建和使用说明

- `make`：编译构建
- `make run`：启动 qemu 并运行
- `make debug`：启动调试
- `make code`：反汇编查看二进制代码
- `make clean`：清理

具体使用请参考具体子项目下的 Makefile 文件。

# 4. 有关 RVOS 的移植

可以参考这篇文章： [《将 RVOS 移植到 MilkV-Duo 上》][1]，介绍了如何将 RVOS 从 32 位扩展为 64 位，以及如何将其移植到一款真正的硬件（Milk-V Duo）上。

也可以看 [这里，收集了网友将本课程代码移植到各种硬件的活动记录][2]。 

# 5. 参考文献

本课程的设计参考了如下网络资源，在此表示感谢 :)

- The Adventures of OS：<https://osblog.stephenmarz.com/index.html>
- mini-riscv-os: <https://github.com/cccriscv/mini-riscv-os>
- Xv6, a simple Unix-like teaching operating system：<https://pdos.csail.mit.edu/6.828/2020/xv6.html>


[1]: https://zhuanlan.zhihu.com/p/691697875
[2]: https://gitee.com/unicornx/riscv-operating-system-mooc/issues/I64EEQ
