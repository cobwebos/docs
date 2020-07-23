---
title: 如何配置 RHEL/CentOS 7 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何配置 RHEL/CentOS 7，以便使用语音 SDK。
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: ba531164e024f96d3bdd23912f3f6e90275edda4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83589731"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>为语音 SDK 配置 RHEL/CentOS 7

语音 SDK 1.10.0 及更高版本正式支持 Red Hat Enterprise Linux (RHEL) 8 x64 和 CentOS 8 x64。 也可以在 RHEL/CentOS 7 x64 上使用语音 SDK，但这需要更新你的系统上的 C++ 编译器（用于 C++ 开发）和共享的 C++ 运行时库。

若要检查 C++ 编译器版本，请运行：

```bash
g++ --version
```

如果安装了编译器，则输出应如下所示：

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

此消息告诉你已安装了 GCC 主版本 4。 此版本并不完全支持语音 SDK 使用的 C++ 11 标准。 尝试使用此 GCC 版本和语音 SDK 头编译 C++ 程序会导致编译错误。

还必须检查共享的 C++ 运行时库 (libstdc++) 的版本。 大多数语音 SDK 都作为原生 C++ 库实现，这意味着它依赖于 libstdc++，不管应用程序是使用哪种语言开发的。

若要查找 libstdc++ 在系统上的位置，请运行：

```bash
ldconfig -p | grep libstdc++
```

vanilla RHEL/CentOS 7 (x64) 上的输出为：

```bash
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

根据此消息，你需要使用以下命令检查版本定义：

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

输出应该是：

```bash
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

语音 SDK 需要 **CXXABI_1.3.9** 和 **GLIBCXX_3.4.21**。 可以通过对 Linux 包中的语音 SDK 库运行 `ldd libMicrosoft.CognitiveServices.Speech.core.so` 来查找此信息。

> [!NOTE]
> 建议在系统上安装的 GCC 版本至少为 5.4.0  ，它具有匹配的运行时库。

## <a name="example"></a>示例

这是一个示例命令集，说明了如何使用 Speech SDK 1.10.0 或更高版本配置 RHEL/CentOS 7 x64 for 开发（c + +、c #、Java、Python）：

### <a name="1-general-setup"></a>1. 常规设置

首先安装所有常规依赖项：

```bash
# Only run ONE of the following two commands
# - for CentOS 7:
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
# - for RHEL 7:
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/7/packages-microsoft-prod.rpm

# Install development tools and libraries
sudo yum update -y
sudo yum groupinstall -y "Development tools"
sudo yum install -y alsa-lib dotnet-sdk-2.1 java-1.8.0-openjdk-devel openssl python3
sudo yum install -y gstreamer1 gstreamer1-plugins-base gstreamer1-plugins-good gstreamer1-plugins-bad-free gstreamer1-plugins-ugly-free
```

### <a name="2-cc-compiler-and-runtime-libraries"></a>2. c/c + + 编译器和运行时库

通过此命令安装必备组件包：

```bash
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
```

> [!NOTE]
> RHEL 7.8 更新中已弃用 libmpc-unixodbc-devel 包。 如果上一个命令的输出包含消息
>
> ```bash
> No package libmpc-devel available.
> ```
>
> 然后，需要从原始源安装所需的文件。 运行以下命令：
>
> ```bash
> curl https://ftp.gnu.org/gnu/mpc/mpc-1.1.0.tar.gz -O
> tar zxf mpc-1.1.0.tar.gz
> mkdir mpc-1.1.0-build && cd mpc-1.1.0-build
> ../mpc-1.1.0/configure --prefix=/usr/local --libdir=/usr/local/lib64
> make -j$(nproc)
> sudo make install-strip
> ```

接下来，更新编译器和运行时库：

```bash
# Build GCC 5.4.0 and runtimes and install them under /usr/local
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip
```

如果更新的编译器和库需要部署在多台计算机上，只需将它们从下复制 `/usr/local` 到其他计算机。 如果只需要运行时库，则中的文件 `/usr/local/lib64` 就足够了。

### <a name="3-environment-settings"></a>3. 环境设置

运行以下命令以完成配置：

```bash
# Set SSL cert file location
# (this is required for any development/testing with Speech SDK)
export SSL_CERT_FILE=/etc/pki/tls/certs/ca-bundle.crt

# Add updated C/C++ runtimes to the library path
# (this is required for any development/testing with Speech SDK)
export LD_LIBRARY_PATH=/usr/local/lib64:$LD_LIBRARY_PATH

# For C++ development only:
# - add the updated compiler to PATH
#   (note, /usr/local/bin should be already first in PATH on vanilla systems)
# - add Speech SDK libraries from the Linux tar package to LD_LIBRARY_PATH
#   (note, use the actual path to extracted files!)
export PATH=/usr/local/bin:$PATH
hash -r # reset cached paths in the current shell session just in case
export LD_LIBRARY_PATH=/path/to/extracted/SpeechSDK-Linux-1.10.0/lib/x64:$LD_LIBRARY_PATH

# For Python: install the Speech SDK module
python3 -m pip install azure-cognitiveservices-speech --user
```

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [关于语音 SDK](speech-sdk.md)
