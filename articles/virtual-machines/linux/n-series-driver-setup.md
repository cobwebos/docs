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
ms.date: 05/02/2017
ms.author: danlep
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: de674af369080ad7eb608608685e293f2326c8e6
ms.openlocfilehash: 69f1363c26d8b5a18ffd5629c6a49c34306dd7c0
ms.contentlocale: zh-cn
ms.lasthandoff: 05/04/2017


---

# <a name="set-up-gpu-drivers-for-n-series-vms-running-linux"></a>为运行 Linux 的 N 系列 VM 安装 GPU 驱动程序

若要利用运行 Linux 的 Azure N 系列 VM 的 GPU 功能，请在每个 VM 上安装 NVIDIA 图形驱动程序。 部署 N 系列 VM 后，本文提供了驱动程序安装步骤。 针对 [Windows VM](../windows/n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) 也提供了驱动程序安装信息。


有关 N 系列 VM 规格、存储容量和磁盘详细信息，请参阅 [GPU Linux VM 大小](sizes-gpu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。 



## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

> [!IMPORTANT]
> 目前，仅在 Azure NC VM 上提供 Linux GPU 驱动程序支持。 

来自 Azure 应用商店的以下分发支持在 N 系列 Linux VM 上运行 NVIDIA 图形驱动程序。

### <a name="nc-vms-tesla-k80-card"></a>NC VM（Tesla K80 卡）
* Ubuntu 16.04 LTS 
* Red Hat Enterprise Linux 7.3 
* 基于 CentOS 的 7.3 

**支持的驱动程序**：NVIDIA CUDA 8.0、driver branch R375。 [安装步骤](#install-cuda-drivers-for-nc-vms)




> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>




## <a name="install-cuda-drivers-for-nc-vms"></a>安装适用于 NC VM 的 CUDA 驱动程序

下面是从 NVIDIA CUDA 工具包 8.0 在 Linux NC VM 上安装 NVIDIA 驱动程序的步骤。 

C 和 C++ 开发人员可以选择安装完整的工具包来生成 GPU 加速应用程序。 有关详细信息，请参阅 [CUDA 安装指南](http://docs.nvidia.com/cuda/cuda-installation-guide-linux/index.html)。


> [!NOTE]
> 在本文发布时，此处提供的 CUDA 驱动程序下载链接是最新的。 有关最新驱动程序，请访问 [NVIDIA](http://www.nvidia.com/) 网站。
>

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

### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 或 Red Hat Enterprise Linux 7.3

> [!IMPORTANT] 
> 由于已知问题，在运行 CentOS 7.3 或 Red Hat Enterprise Linux 7.3 的 NC24r VM 上安装 NVIDIA CUDA 驱动程序将失败。
>

首先，获取更新。 

```bash
sudo yum update

sudo reboot
```

重新连接到 VM 并使用以下命令继续安装：

```bash
sudo yum install kernel-devel

sudo rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm

sudo yum install dkms

CUDA_REPO_PKG=cuda-repo-rhel7-8.0.61-1.x86_64.rpm

wget http://developer.download.nvidia.com/compute/cuda/repos/rhel7/x86_64/${CUDA_REPO_PKG} -O /tmp/${CUDA_REPO_PKG}

sudo rpm -ivh /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo yum install cuda-drivers
```

安装可能需要几分钟。 若要安装完整的 CUDA 工具包，请键入：

```bash
sudo yum install cuda
```

重新启动 VM，然后继续验证安装。


### <a name="verify-driver-installation"></a>验证驱动程序安装


若要查询 GPU 设备状态，请建立到 VM 的 SSH 连接，然后运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

将显示类似于下面的输出：

![NVIDIA 设备状态](./media/n-series-driver-setup/smi.png)


### <a name="cuda-driver-updates"></a>CUDA 驱动程序更新

在部署后，建议你定期更新 CUDA 驱动程序。

#### <a name="ubuntu-1604-lts"></a>Ubuntu 16.04 LTS

```bash
sudo apt-get update

sudo apt-get upgrade -y

sudo apt-get dist-upgrade -y

sudo apt-get install cuda-drivers
```

在更新完成后，重新启动 VM。

#### <a name="centos-73-or-red-hat-enterprise-linux-73"></a>CentOS 7.3 或 Red Hat Enterprise Linux 7.3

```bash
sudo yum update
```

在更新完成后，重新启动 VM。



## <a name="troubleshooting"></a>故障排除

* Ubuntu 16.04 LTS 上运行 4.4.0-75 Linux 内核的 Azure N 系列 VM 上的 CUDA 驱动程序存在已知问题。 若要在升级内核时保持驱动程序正常工作，请至少升级到内核版本 4.4.0-77。 



## <a name="next-steps"></a>后续步骤

* 有关 N 系列 VM 上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）

* 若要捕获安装了 NVIDIA 驱动程序的 Linux VM 映像，请参阅[如何通用化和捕获 Linux 虚拟机](capture-image.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。
