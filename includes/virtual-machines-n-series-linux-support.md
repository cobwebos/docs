---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: cynthn
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 02/11/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: b8aba410cd447f14fcce89fee93c5f6a253a34ce
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2019
ms.locfileid: "56101775"
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 驱动程序

仅下表列出的 Linux 分发中支持适用于 NC、NCv2、NCv3、ND 和 NDv2 系列 VM 的 NVIDIA CUDA 驱动程序（对 NV 系列为可选项）。 本文发布时，CUDA 驱动程序信息为最新版本。 有关最新 CUDA 驱动程序，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。 确保安装或升级到最新 CUDA 驱动程序分发软件包。 

> [!TIP]
> 作为一种在 Linux VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Ubuntu 16.04 LTS 或 CentOS 7.4 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。

| 分发 | 驱动程序 |
| --- | -- | 
| Ubuntu 16.04 LTS、18.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3、7.4、7.5、7.6<br/><br/> 基于 CentOS 的 7.3、7.4、7.5、7.6、基于 CentOS 的 7.4 HPC | NVIDIA CUDA 10.0，驱动程序分支为 R410 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或用于虚拟应用程序的 NV 和 NVv2 系列 VM，重新分发 NVIDIA GRID 驱动程序安装程序。 请仅在下表所列操作系统上的 Azure NV VM 上安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID Virtual GPU Software 的许可。 不需要设置 NVIDIA vGPU 软件许可证服务器。

| 分发 | 驱动程序 |
| --- | -- |
| Ubuntu 16.04 LTS、18.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3、7.4、7.5、7.6<br/><br/>基于 CentOS 的 7.3、7.4、7.5、7.6 | NVIDIA GRID 7.1，驱动程序分支为 R410|

> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>
