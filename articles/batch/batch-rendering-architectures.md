---
title: Azure 渲染参考体系结构 - Azure Batch
description: 使用 Azure Batch 和其他 Azure 服务以迸发到云的方式扩展本地渲染场时对应的体系结构
services: batch
ms.service: batch
author: davefellows
manager: jeconnoc
ms.author: lahugh
ms.date: 02/07/2019
ms.topic: conceptual
ms.custom: seodec18
ms.openlocfilehash: ae4680c948ce8e1efd32207dc37821d61182f2d8
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "60774139"
---
# <a name="reference-architectures-for-azure-rendering"></a>Azure 渲染的参考体系结构

本文介绍的高级体系结构图对应于将本地渲染场扩展或“迸发”到 Azure 时的场景。 示例显示了适合 Azure 计算、网络和存储服务的不同选项。

## <a name="hybrid-with-nfs-or-cfs"></a>与 NFS 或 CFS 的混合

下图显示了一个混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 输入和输出文件：使用 Azure VM 的 NFS 或 CFS，通过 Azure 文件同步或 RSync 与本地存储同步。 也可使用以下命令：使用 NFS 从本地 NAS 设备输入或输出文件的 Avere vFXT。

  ![云迸发 - 与 NFS 或 CFS 的混合](./media/batch-rendering-architectures/hybrid-nfs-cfs-avere.png)

## <a name="hybrid-with-blobfuse"></a>与 Blobfuse 的混合

下图显示了一个混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 输入和输出文件：Blob 存储，通过 Azure Blobfuse 装载到计算资源。

  ![云迸发 - 与 Blobfuse 的混合](./media/batch-rendering-architectures/hybrid-blob-fuse.png)

## <a name="hybrid-compute-and-storage"></a>混合计算和存储

下图显示了一个完全连接的针对计算和存储的混合场景，其中包括以下 Azure 服务：

* **计算** - Azure Batch 池或虚拟机规模集。

* **网络** - 本地：Azure ExpressRoute 或 VPN。 Azure：Azure VNet。

* **存储** - 跨界：Avere vFXT。 通过 Azure Data Box 以可选方式将本地文件存档到 Blob 存储，或通过本地 Avere vFXT 加速 NAS。

  ![云迸发 - 混合计算和存储](./media/batch-rendering-architectures/hybrid-compute-storage-avere.png)


## <a name="next-steps"></a>后续步骤

* 详细了解如何将[渲染管理器](batch-rendering-render-managers.md)与 Azure Batch 配合使用。

* 详细了解[在 Azure 中渲染](batch-rendering-service.md)的选项。