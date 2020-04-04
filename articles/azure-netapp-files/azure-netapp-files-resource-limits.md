---
title: Azure NetApp 文件的资源限制 | Microsoft Docs
description: 描述 Azure NetApp 文件资源的限制以及如何请求资源限制增加。
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
ms.date: 04/03/2020
ms.author: b-juche
ms.openlocfilehash: ac660b20d519e49e832e979603f763fa672757a5
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637398"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp 文件的资源限制

了解 Azure NetApp 文件的资源限制，可帮助你管理卷。

## <a name="resource-limits"></a>资源限制

下表描述了 Azure NetApp 文件的资源限制：

|  资源  |  默认限制  |  可通过支持请求进行调节  |
|----------------|---------------------|--------------------------------------|
|  每个 Azure 区域的净应用帐户数   |  10    |  是   |
|  每个 NetApp 帐户的容量池数   |    25     |   是   |
|  每个容量池的卷数     |    500   |    是     |
|  每个卷的快照数       |    255     |    否        |
|  每个 Azure 虚拟网络委派给 Azure NetApp 文件（Microsoft.NetApp/卷）的子网数    |   1   |    否    |
|  具有 Azure NetApp 文件的 VNet（包括立即对等 VNet）中的已使用 IP 数   |    1000   |    否   |
|  单个容量池的最小大小   |  4 TiB     |    否  |
|  单个容量池的最大大小    |  500 TiB   |   否   |
|  单个卷的最小大小    |    100 GiB    |    否    |
|  单个卷的最大大小     |    100 TiB    |    否    |
|  单个文件的最大大小     |    16 TiB    |    否    |    
|  单个目录的最大大小      |    320 MB    |    否    |    
|  每个卷的最大文件数 （[最大文件](#maxfiles)）     |    1 亿    |    是    |    

有关详细信息，请参阅[容量管理常见问题解答](azure-netapp-files-faqs.md#capacity-management-faqs)。

## <a name="maxfiles-limits"></a>最大文件限制<a name="maxfiles"></a> 

Azure NetApp 文件卷具有称为*maxfile*的限制。 最大文件限制是卷可以包含的文件数。 Azure NetApp 文件卷的最大文件限制根据卷的大小（配额）进行索引。 卷的最大文件限制以预配卷大小的每个 TiB 2000 万个文件的速率增加或减少。 

该服务根据其预配大小动态调整卷的最大文件限制。 例如，最初配置大小为 1 TiB 的卷的最大文件限制为 2000 万。 随后对卷大小的更改将导致根据以下规则自动调整 maxfiles 限制： 

|    体积大小（配额）     |  最大文件限制的自动调整    |
|----------------------------|-------------------|
|    < 1 TiB                 |    2000万     |
|    >= 1 TiB，但 < 2 TiB    |    4000万     |
|    >= 2 TiB，但 < 3 TiB    |    6000 万     |
|    >= 3 TiB，但 < 4 TiB    |    8000万     |
|    >= 4 TiB                |    1 亿    |

对于任何卷大小，您可以启动[支持请求](#limit_increase)，将最大文件限制增加到 1 亿个。

## <a name="request-limit-increase"></a>请求限制增加<a name="limit_increase"></a> 

您可以创建 Azure 支持请求，以便从上表中增加可调整的限制。 

从 Azure 门户导航平面： 

1. 单击 **"帮助 + 支持**"。
2. 单击 **= 新的支持请求**。
3. 在“基本信息”选项卡中提供以下信息： 
    1. 问题类型：选择**服务和订阅限制（配额）。**
    2. 订阅：为需要增加配额的资源选择订阅。
    3. 配额类型：选择**存储：Azure NetApp 文件限制**。
    4. 单击 **"下一步：解决方案**"。
4. 在"详细信息"选项卡上：
    1. 在"描述"框中，为相应的资源类型提供以下信息：

        |  资源  |    父资源      |    请求的新限制     |    增加配额的原因       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  Account |  *订阅 ID*   |  *请求的新最大**帐**号*    |  *是什么方案或用例提示了请求？*  |
        |  池    |  *订阅 ID，帐户 URI*  |  *请求的新最大**池**号*   |  *是什么方案或用例提示了请求？*  |
        |  数据量(Volume)  |  *订阅 ID、帐户 URI、池 URI*   |  *请求的新最大**卷**数*     |  *是什么方案或用例提示了请求？*  |
        |  最大文件  |  *订阅 ID、帐户 URI、池 URI、卷 URI*   |  *请求的新**最大文件**编号*     |  *是什么方案或用例提示了请求？*  |    

    2. 指定适当的支持方法并提供合同信息。

    3. 单击 **"下一步：查看 + 创建**"以创建请求。 


## <a name="next-steps"></a>后续步骤  

- [了解 Azure NetApp 文件的存储层次结构](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp 文件的成本模型](azure-netapp-files-cost-model.md)
