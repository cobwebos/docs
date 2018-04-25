---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 04/03/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: e925dba3805ec8994aeba730e325c407468a5c87
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2018
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA CUDA 驱动程序

在本文发布时，下表中的 CUDA 驱动程序信息是最新的。 有关最新 CUDA 驱动程序，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。 确保安装或升级到最新 CUDA 驱动程序分发软件包。 

> [!TIP]
> 作为一种在 Linux VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Ubuntu 16.04 LTS 或 CentOS 7.4 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。

| 分发 | 驱动程序 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 或 7.4<br/><br/> CentOS 7.3 或 7.4，基于 CentOS 的 7.4 HPC | NVIDIA CUDA 9.1，驱动程序分支为 R390 |

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驱动程序

Microsoft 将重新分发适用于 NV VM 的 NVIDIA GRID 驱动程序安装程序。 在 Azure NV VM 上仅安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID 虚拟 GPU 软件的授权。

| 分发 | 驱动程序 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>基于 CentOS 的 7.3 | NVIDIA GRID 6.0，驱动程序分支为 R390|



> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>
