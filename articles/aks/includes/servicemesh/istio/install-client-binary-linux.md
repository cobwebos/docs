---
author: paulbouwer
ms.topic: include
ms.date: 10/02/2020
ms.author: pabouwer
ms.openlocfilehash: 164844a9da09563f8fbefe7ec60aff7eb05ace2d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "91666688"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下载并安装 Istio istioctl 客户端二进制文件

在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]上的基于 bash 的 shell 中，使用 `curl` 下载 Istio 发行版，然后使用 `tar` 进行解压缩，如下所示：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.7.3

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istioctl-$ISTIO_VERSION-linux-amd64.tar.gz" | tar xz
```

`istioctl`客户端二进制文件在客户端计算机上运行，并允许在 AKS 群集中安装和管理 Istio。 使用以下命令在 Linux 或[适用于 Linux 的 Windows 子系统][install-wsl]上的基于 bash 的 shell 中安装 Istio `istioctl` 客户端二进制文件。 这些命令可将 `istioctl` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
sudo mv ./istioctl /usr/local/bin/istioctl
sudo chmod +x /usr/local/bin/istioctl
```

如果想要通过命令行完成 `istioctl` 客户端二进制文件，则按如下所示进行设置：

```bash
# Generate the bash completion file and source it in your current shell
mkdir -p ~/completions && istioctl collateral --bash -o ~/completions
source ~/completions/istioctl.bash

# Source the bash completion file in your .bashrc so that the command-line completions
# are permanently available in your shell
echo "source ~/completions/istioctl.bash" >> ~/.bashrc
```

<!-- LINKS - external -->
[install-wsl]: /windows/wsl/install-win10
