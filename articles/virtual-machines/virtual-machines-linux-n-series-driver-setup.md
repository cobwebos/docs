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
ms.date: 12/07/2016
ms.author: danlep
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: 0d7eba02757fb1b2263cf11c561b374eab837f21
ms.lasthandoff: 03/03/2017


---
# <a name="set-up-gpu-drivers-for-n-series-linux-vms"></a>为 N 系列 Linux VM 安装 GPU 驱动程序
若要利用运行支持的 Linux 分发的 Azure N 系列 VM 的 GPU 功能，部署后必须在每个 VM 上安装 NVIDIA 图形驱动程序。 本文同样适用于 [Windows VM](virtual-machines-windows-n-series-driver-setup.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)。

有关 N 系列 VM 规格、存储容量和磁盘详细信息，请参阅[虚拟机的大小](virtual-machines-linux-sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)。



## <a name="supported-gpu-drivers"></a>支持的 GPU 驱动程序


> [!NOTE]
> 目前，仅在运行 Ubuntu Server 16.04 LTS 的 Azure NC VM 上提供 Linux GPU 支持。

### <a name="nvidia-tesla-drivers-for-nc-vms"></a>适用于 NC VM 的 NVIDIA Tesla 驱动程序

* [Ubuntu 16.04 LTS](https://go.microsoft.com/fwlink/?linkid=836899)（.run 自解压安装程序）

## <a name="tesla-driver-installation-on-ubuntu-1604-lts"></a>在 Ubuntu 16.04 LTS 上安装 Tesla 驱动程序

1. 与 Azure N 系列 VM 建立 SSH 连接。

2. 若要验证系统是否具有支持 CUDA 的 GPU，请运行以下命令：

    ```bash
    lspci | grep -i NVIDIA
    ```
    你将看到类似于以下示例（显示 NVIDIA Tesla K80 卡）的输出：

    ![lspci 命令输出](./media/virtual-machines-linux-n-series-driver-setup/lspci.png)

3. 下载分发的驱动程序的 .run 文件。 以下示例命令将 Ubuntu 16.04 LTS Tesla 驱动程序下载到 /tmp 目录中：

    ```bash
    wget -O /tmp/NVIDIA-Linux-x86_64-367.48.run https://go.microsoft.com/fwlink/?linkid=836899
    ```

4. 如果需要在系统上安装 `gcc` 和 `make`（Tesla 驱动程序所必需），请键入以下命令：

    ```bash
    sudo apt-get update
    
    sudo apt install gcc

    sudo apt install make
    ```

4. 更改到包含驱动程序安装程序的目录并运行如下命令：

    ```bash
    chmod +x NVIDIA-Linux-x86_64-367.48.run
    
    sudo sh ./NVIDIA-Linux-x86_64-367.48.run
    ```

## <a name="verify-driver-installation"></a>验证驱动程序安装


若要查询 GPU 设备状态，请运行与驱动程序一起安装的 [nvidia-smi](https://developer.nvidia.com/nvidia-system-management-interface) 命令行实用工具。 

![NVIDIA 设备状态](./media/virtual-machines-linux-n-series-driver-setup/smi.png)

## <a name="optional-installation-of-nvidia-cuda-toolkit-on-ubuntu-1604-lts"></a>Ubuntu 16.04 LTS 上 NVIDIA CUDA 工具包的可选安装

可以选择在运行 Ubuntu 16.04 LTS 的 NC VM 上安装 NVIDIA CUDA 工具包 8.0。 除了 GPU 驱动程序外，该工具包还为生成 GPU 加速应用程序的 C 和 C++ 开发人员提供全面的开发环境。

若要安装 CUDA 工具包，请运行如下命令：

```bash
CUDA_REPO_PKG=cuda-repo-ubuntu1604_8.0.44-1_amd64.deb

wget -O /tmp/${CUDA_REPO_PKG} http://developer.download.nvidia.com/compute/cuda/repos/ubuntu1604/x86_64/${CUDA_REPO_PKG} 

sudo dpkg -i /tmp/${CUDA_REPO_PKG}

rm -f /tmp/${CUDA_REPO_PKG}

sudo apt-get update

sudo apt-get install cuda-drivers
```

安装可能需要几分钟。

## <a name="next-steps"></a>后续步骤

* 有关 N 系列 VM 上的 NVIDIA GPU 的详细信息，请参阅：
    * [NVIDIA Tesla K80](http://www.nvidia.com/object/tesla-k80.html)（适用于 Azure NC VM）
    * [NVIDIA Tesla M60](http://www.nvidia.com/object/tesla-m60.html)（适用于 Azure NV VM）


