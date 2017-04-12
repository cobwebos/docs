---
title: "适用于 Linux 的 Azure N 系列驱动程序安装 | Microsoft Docs"
description: "如何为 Azure 中运行 Linux 的 N 系列 VM 安装 NVIDIA GPU 驱动程序"
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: d91695d0-64b9-4e6b-84bd-18401eaecdde
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/10/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: e7f6c840be3a284f635114287a69c151f671531d
ms.lasthandoff: 04/03/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>为运行 Linux 的 N 系列 VM 安装 GPU 驱动程序

若要利用运行支持的 Linux 分发的 Azure N 系列 VM 的 GPU 功能，部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 针对 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 也提供了驱动程序安装信息。


> [!IMPORTANT]
> 目前，仅在运行 Ubuntu Server 16.04 LTS 的 Azure NC VM 上提供 Linux GPU 支持。
> 

有关 N 系列 VM 规格、存储容量和磁盘详细信息，请参阅[虚拟机的大小](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 另请参阅 [N 系列 VM 的一般注意事项](#general-considerations-for-n-series-vms)。



## <a name="install-nvidia-cuda-drivers"></a>安装 NVIDIA CUDA 驱动程序

下面是从 NVIDIA CUDA 工具包 8.0 在 Linux NC VM 上安装 NVIDIA 驱动程序的步骤。 C 和 C++ 开发人员可以选择安装完整的工具包来生成 GPU 加速应用程序。 有关详细信息，请参阅 [CUDA 安装指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 此处提供的驱动程序下载链接在本文发布时是最新的。 有关最新驱动程序，请访问 [NVIDIA](http://www.nvidia.com/) 网站。

若要安装 CUDA 工具包，请建立到每个虚拟机的 SSH 连接。 若要验证系统是否具有支持 CUDA 的 GPU，请运行以下命令：

```bash
lspci | grep -i NVIDIA
```
你将看到类似于以下示例（显示 NVIDIA Tesla K80 卡）的输出：

![lspci 命令输出](./media/n-series-driver-setup/lspci.png)

然后，运行特定于你的分发的命令。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.61-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers

```
安装可能需要几分钟。

若要安装完整的 CUDA 工具包，请键入：

```bash
sudo apt-get install cuda
```

重新启动 VM，然后继续验证安装。

## <a name="verify-driver-installation"></a>验证驱动程序安装


若要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，然后运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)

## <a name="cuda-driver-updates"></a>CUDA 驱动程序更新

在部署后，建议你定期更新 CUDA 驱动程序。

### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

在更新完成后，重新启动 VM。


[!INCLUDE [virtual-machines-n-series-considerations](../../../includes/virtual-machines-n-series-considerations.md)]

* 我们不建议在 Ubuntu NC VM 上安装 X server 或使用 nouveau 驱动程序的其他系统。 在安装 NVIDIA GPU 驱动程序之前，需要禁用 nouveau 驱动程序。  

* 如果希望捕获你在其中安装了 NVIDIA 驱动程序的 Linux VM 的映像，请参阅[如何通用化和捕获 Linux 虚拟机](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。

## <a name="next-steps"></a>后续步骤

* 有关 N 系列 VM 上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）


