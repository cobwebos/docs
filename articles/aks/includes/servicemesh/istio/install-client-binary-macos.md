---
author: paulbouwer
ms.service: container-service
ms.topic: include
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 06479b4396ab26c280cc6246d774bc30b5ea1c76
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530389"
---
## <a name="download-and-install-the-istio-istioctl-client-binary"></a>下载并安装 Istio istioctl 客户端二进制文件

在 MacOS 上基于 bash 的 shell 中，使用 `curl` 下载 Istio 版本，并按如下所示提取 `tar`：

```bash
# Specify the Istio version that will be leveraged throughout these instructions
ISTIO_VERSION=1.3.2

curl -sL "https://github.com/istio/istio/releases/download/$ISTIO_VERSION/istio-$ISTIO_VERSION-osx.tar.gz" | tar xz
```

@No__t_0 客户端二进制文件在客户端计算机上运行，并允许与 Istio 服务网格交互。 使用以下命令在 MacOS 上的基于 bash 的 shell 中安装 Istio `istioctl` 客户端二进制文件。 这些命令可将 `istioctl` 客户端二进制文件复制到 `PATH` 中的标准用户程序位置。

```bash
cd istio-$ISTIO_VERSION
sudo cp ./bin/istioctl /usr/local/bin/istioctl
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