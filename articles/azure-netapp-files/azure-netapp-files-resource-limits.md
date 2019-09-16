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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 15d0a584d88045f6020162a88124cd9d6a4735bf
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2019
ms.locfileid: "70984003"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="resource-limits"></a>资源限制

下表描述了 Azure NetApp 文件的资源限制：

|  Resource  |  默认限制  |  可通过支持请求进行调整  |
|----------------|---------------------|--------------------------------------|
|  每个 Azure 订阅的 NetApp 帐户数   |  10    |  是   |
|  每个 NetApp 帐户的容量池数   |    25     |   是   |
|  每个容量池的卷数量     |    500   |    是     |
|  每个卷的快照数       |    255     |    否        |
|  每个 Azure 虚拟网络委托给 Azure NetApp 文件（Microsoft NetApp/卷）的子网数    |   1   |    否    |
|  可以访问 Azure NetApp 文件的 VNet 中的 Ip 数（包括对等互连 Vnet）   |    1000   |    是   |
|  单个容量池的最小大小   |  4 TiB     |    否  |
|  单个容量池的最大大小    |  500 TiB   |   否   |
|  单个卷的最小大小    |    100 GiB    |    否    |
|  单个卷的最大大小     |    100 TiB    |    否       |
|  每个卷的最大文件数（inode）     |    50000000    |    否    |    

## <a name="request-limit-increase"></a>请求限制增加 

可以创建 Azure 支持请求，增加上表中的可调整限制。 

从 Azure 门户导航平面： 

1. 单击 "**帮助 + 支持**"。
2. 单击 " **+ 新建支持请求**"。
3. 在 "基本信息" 选项卡上，提供以下信息： 
    1. 问题类型：选择**服务和订阅限制（配额）** 。
    2. 订阅选择需要增加配额的资源的订阅。
    3. 配额类型：选择**存储：Azure NetApp 文件限制**。
    4. 单击“下一步:**解决方法**。
4. 在 "详细信息" 选项卡上：
    1. 在 "说明" 框中，为相应的资源类型提供以下信息：

        |  Resource  |    父资源      |    请求的新限制     |    配额增加的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  帐户 |  *订阅 ID*   |  *请求的新的最大**帐户**编号*    |  *哪个方案或用例提示请求？*  |
        |  池    |  *订阅 ID，帐户 URI*  |  *请求的新的最大**池**编号*   |  *哪个方案或用例提示请求？*  |
        |  卷  |  *订阅 ID、帐户 URI、池 URI*   |  *请求的新的最大**卷**号*     |  *哪个方案或用例提示请求？*  |

    2. 指定相应的支持方法并提供你的协定信息。

    3. 单击“下一步:**查看 + create**以创建请求。 


## <a name="next-steps"></a>后续步骤  

- [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
