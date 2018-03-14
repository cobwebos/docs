---
title: "include 文件"
description: "include 文件"
services: virtual-machines-linux
author: dlepow
ms.service: virtual-machines-linux
ms.topic: include
ms.date: 03/01/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 22d37ca30f1319f46a52b96be1c527f6f56719ab
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2018
---
## <a name="supported-distributions-and-drivers"></a>支持的分发和驱动程序

### <a name="nc-ncv2-ncv3-and-nd-series---nvidia-cuda-drivers"></a>NC、NCv2、NCv3 和 ND 系列 - NVIDIA CUDA 驱动程序
| 分发 | 驱动程序 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/> Red Hat Enterprise Linux 7.3 或 7.4<br/><br/> CentOS 7.3 或 7.4 | NVIDIA CUDA 9.1，驱动程序分支为 R390 |

> [!IMPORTANT]
> 确保安装或升级到最新 CUDA 驱动程序分发软件包。 早于版本 R390 驱动程序可能会遇到与更新的 Linux 内核的问题。
>

### <a name="nv-series---nvidia-grid-drivers"></a>NV 系列 - NVIDIA GRID 驱动程序

| 分发 | 驱动程序 |
| --- | --- | 
| Ubuntu 16.04 LTS<br/><br/>Red Hat Enterprise Linux 7.3<br/><br/>基于 CentOS 的 7.3 | NVIDIA GRID 5.2，驱动程序分支为 R384|

> [!NOTE]
> Microsoft 将重新分发适用于 NV VM 的 NVIDIA GRID 驱动程序安装程序。 在 Azure NV VM 上仅安装这些 GRID 驱动程序。 这些驱动程序包括 Azure 中 GRID 虚拟 GPU 软件的授权。
>

> [!WARNING] 
> 在 Red Hat 产品上安装第三方软件可能会影响 Red Hat 支持条款。 请参阅 [Red Hat 知识库文章](https://access.redhat.com/articles/1067)。
>
