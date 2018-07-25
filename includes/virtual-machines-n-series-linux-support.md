---
title: include 文件
description: include 文件
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 05/29/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 1813367a2d143f75fb51a3160dd00219c709c57b
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2018
ms.locfileid: "37935772"
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 驱动程序

仅下表列出的 Linux 分发中支持适用于 NC、NCv2、NCv3 和 ND 系列 VM 的 NVIDIA CUDA 驱动程序（对 NV 系列为可选项）。 本文发布时，CUDA 驱动程序信息为最新版本。 有关最新 CUDA 驱动程序，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。 确保安装或升级到最新 CUDA 驱动程序分发软件包。 

> [!TIP]
> 作为一种在 Linux VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Ubuntu 16.04 LTS 或 CentOS 7.4 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。

| 分发 | 驱动程序 |
| --- | -- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 或 7.4<br/><br/> 基于 CentOS 的 7.3 或 7.4 HPC、基于 CentOS 的 7.4 HPC | NVIDIA CUDA 9.1，驱动程序分支为 R390 |

### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或用于虚拟应用程序的 NV 系列 VM，分发 NVIDIA GRID 驱动程序安装程序。 请仅在下表所列分发上的 Azure NV VM 中安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID 虚拟 GPU 软件的授权。

| 分发 | 驱动程序 |
| --- | -- |
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3 或 7.4<br/><br/>基于 CentOS 的 7.3 或 7.4 | NVIDIA GRID 6.2，驱动程序分支为 R390|



> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>
