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
ms.openlocfilehash: 86f687bd4fd8be93efbf77c883c723046c2c96d8
ms.sourcegitcommit: 83610f637914f09d2a87b98ae7a6ae92122a02f1
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/13/2020
ms.locfileid: "91997930"
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nvidia-cuda-drivers"></a>NVIDIA CUDA 驱动程序

仅下表列出的 Linux 分发中支持适用于 NC、NCv2、NCv3、ND 和 NDv2 系列 VM 的 NVIDIA CUDA 驱动程序（对 NV 系列为可选项）。 本文发布时，CUDA 驱动程序信息为最新版本。 有关最新的 CUDA 驱动程序和支持的操作系统，请访问 [NVIDIA](https://developer.nvidia.com/cuda-zone) 网站。 确保安装或升级到最新 CUDA 驱动程序分发软件包。 

> [!TIP]
> 作为一种在 Linux VM 上手动安装 CUDA 驱动程序的替代方法，可以部署 Azure [数据科学虚拟机](../articles/machine-learning/data-science-virtual-machine/overview.md)映像。 用于 Ubuntu 16.04 LTS 或 CentOS 7.4 的 DSVM 版本预安装 NVIDIA CUDA 驱动程序、CUDA 深度神经网络库和其他工具。


### <a name="nvidia-grid-drivers"></a>NVIDIA GRID 驱动程序

Microsoft 为用作虚拟工作站或虚拟应用程序的 NV 和 NVv3 系列 Vm 重新分发 NVIDIA 网格驱动程序安装程序。 请仅在下表所列操作系统上的 Azure NV VM 上安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID Virtual GPU Software 的许可。 不需要设置 NVIDIA vGPU 软件许可证服务器。

Azure 重新分发的网格驱动程序不适用于非 NV 系列 Vm，如 NC、NCv2、NCv3、ND 和 NDv2 系列 Vm。

| 分发 | 驱动程序 |
| --- | -- |
|Ubuntu 18.04 LTS<br/><br/>Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.6 到7.8、8.0、8。1<br/><br/>基于 CentOS 的7.6、7.7、8.0、8 (1911) <br/><br/>SUSE Linux Enterprise Server 12 SP2 | NVIDIA GRID 11.1、driver branch [bk-r450](https://go.microsoft.com/fwlink/?linkid=874272) ( .exe) |

> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>
