---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 介绍 Azure NetApp 文件资源的限制，包括 NetApp 帐户、容量池、卷、快照和委派子网的限制。
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826379"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="resource-limits"></a>资源限制

下表描述 Azure NetApp 文件的资源限制：

|  资源  |  默认限制  |  通过支持请求可调整  |
|----------------|---------------------|--------------------------------------|
|  每个 Azure 订阅的 NetApp 帐户数   |  10    |  “是”   |
|  每个 NetApp 帐户的容量池数   |    25     |   “是”   |
|  每个容量池的卷数     |    500   |    “是”     |
|  每个卷的快照数       |    255     |    “否”        |
|  委派给 Azure NetApp 文件 (Microsoft.NetApp/volumes) 每个 Azure 虚拟网络的子网数    |   第   |    “否”    |
|  最大 Vm 数 （包括对等的 Vnet），可以连接到的卷     |    1000   |    “否”   |
|  单个容量池的最小大小   |  4 TiB     |    “否”  |
|  单个容量池的最大大小    |  500 TiB   |   “否”   |
|  是单个卷的最小大小    |    100 GiB    |    “否”    |
|  最大分配配额的单个卷 *   |   92 TiB   |    “否”   |
|  最大大小为单个卷 *     |    100 TiB    |    “否”       |

* 卷可以手动创建或大小调整为最常 92 TiB。 但是，卷可以增长的超额的方案中最多 100 个 TiB。 请参阅[Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)有关容量超额的详细信息。 

## <a name="request-limit-increase"></a>请求增加限制 

可以创建 Azure 支持请求来提高从上表可调整的限制。 

从 Azure 门户中导航平面： 

1. 单击**帮助 + 支持**。
2. 单击 **+ 新建支持请求**。
3. 在基础知识选项卡，提供以下信息： 
    1. 问题类型：选择**服务和订阅限制 （配额）**。
    2. 订阅:选择需要增加配额的资源的订阅。
    3. 配额类型：选择**存储：Azure 的 NetApp 文件限制**。
    4. 单击“下一步:**解决方法**。
4. 在详细信息选项卡：
    1. 在描述框中，提供相应的资源类型的以下信息：

        |  资源  |    父资源      |    请求新的限制     |    增加配额的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帐户 |  *订阅 ID*   |  *请求新的最大**帐户**数*    |  *哪些方案或用例提示请求？*  |
        |  池    |  *订阅 ID，帐户 URI*  |  *请求新的最大**池**数*   |  *哪些方案或用例提示请求？*  |
        |  卷  |  *订阅 ID，帐户的 URI，池 URI*   |  *请求新的最大**卷**数*     |  *哪些方案或用例提示请求？*  |

    2. 指定相应的支持方法并提供你的协定信息。

    3. 单击“下一步:**查看 + 创建**来创建请求。 


## <a name="next-steps"></a>后续步骤  

- [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
- [用于 Azure 的 NetApp 文件的成本的模型](azure-netapp-files-cost-model.md)
