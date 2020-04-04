---
title: 如何配置 RHEL/CentOS 7 - 语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何配置 RHEL/CentOS 7 以便可以使用语音 SDK。
services: cognitive-services
author: pankopon
manager: jhakulin
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: pankopon
ms.openlocfilehash: dc09d517d95b5a3f2a88504a14f1451d1de5ffc9
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80639159"
---
# <a name="configure-rhelcentos-7-for-speech-sdk"></a>为语音 SDK 配置 RHEL/CentOS 7

红帽企业 Linux （RHEL） 8 x64 和 CentOS 8 x64 得到语音 SDK 版本 1.10.0 和更高版本的正式支持。 也可以在 RHEL/CentOS 7 x64 上使用语音 SDK，但这需要更新C++编译器（用于C++开发）和系统上的共享C++运行时库。

要检查C++编译器版本，请运行：

```bash
g++ --version
```

如果安装了编译器，则输出应如下所示：

```bash
g++ (GCC) 4.8.5 20150623 (Red Hat 4.8.5-39)
```

此消息让您知道已安装 GCC 主要版本 4。 此版本没有完全支持语音 SDK 使用的 C++ 11 标准。 尝试使用此 GCC 版本和语音 SDK 标头编译C++程序将导致编译错误。

检查共享C++运行时库 （libstdc#） 的版本也很重要。 大多数语音 SDK 都是作为本机C++库实现的，这意味着它依赖于 libstdc®，而不管您使用何种语言来开发应用程序。

要查找系统上 libstdc® 的位置，请运行：

```bash
ldconfig -p | grep libstdc++
```

香草 RHEL/CentOS 7 （x64） 上的输出是：

```
libstdc++.so.6 (libc6,x86-64) => /lib64/libstdc++.so.6
```

根据此消息，您需要使用此命令检查版本定义：

```bash
strings /lib64/libstdc++.so.6 | egrep "GLIBCXX_|CXXABI_"
```

输出应为：

```
...
GLIBCXX_3.4.19
...
CXXABI_1.3.7
...
```

语音 SDK 需要**CXXABI_1.3.9**和**GLIBCXX_3.4.21**。 您可以通过在 Linux 包的`ldd libMicrosoft.CognitiveServices.Speech.core.so`语音 SDK 库上运行来查找此信息。

> [!NOTE]
> 建议安装在系统上的 GCC 版本至少为**5.4.0，** 具有匹配的运行时库。

## <a name="example"></a>示例

这是一个示例命令，说明如何使用语音 SDK 1.10.0 或更高版本配置 RHEL/CentOS 7 x64 用于开发（C++、C#、Java、Python）：

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

# Build GCC 5.4.0 and runtimes and install them under /usr/local
sudo yum install -y gmp-devel mpfr-devel libmpc-devel
curl https://ftp.gnu.org/gnu/gcc/gcc-5.4.0/gcc-5.4.0.tar.bz2 -O
tar jxf gcc-5.4.0.tar.bz2
mkdir gcc-5.4.0-build && cd gcc-5.4.0-build
../gcc-5.4.0/configure --enable-languages=c,c++ --disable-bootstrap --disable-multilib --prefix=/usr/local
make -j$(nproc)
sudo make install-strip

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
