---
title: 将 Azure 资源移到另一区域
description: 概述如何跨 Azure 区域移动 Azure 资源。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485203"
---
# <a name="moving-azure-resources-across-regions"></a>跨区域移动 Azure 资源

本文提供有关跨 Azure 区域移动 Azure 资源的信息。

Azure 地理位置、区域和可用性区域构成了 Azure 全局基础结构的基础。 Azure [地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)通常包含两个或更多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 区域是地理位置内的一个区域，包含可用性区域和多个数据中心。 

在特定的 Azure 区域中部署资源后，你可能会出于以下多种原因，需要将资源移到其他区域。

- **与区域启动对齐**：将资源移动到以前不可用的新引入的 Azure 区域。
- **对齐服务/功能**：移动资源以利用特定区域中可用的服务或功能。
- **响应业务发展**：将资源移动到区域，以应对业务变化，如合并或收购。
- **对齐以进行接近**：将资源移动到企业本地区域。
- **满足数据要求**：移动资源以符合数据驻留要求或数据分类需求。 [了解详情](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **响应部署要求**：移动错误部署的资源，或根据容量需求移动。 
- **响应退役**：因区域退役而转移资源。

## <a name="move-process"></a>移动过程

实际移动过程取决于要移动的资源。 但是，有一些通用的关键步骤：

- **验证先决条件**：先决条件包括确保所需的资源在目标区域中可用，检查您是否有足够的配额，以及验证订阅是否可以访问目标区域。
- **分析依赖项**：您的资源可能依赖于其他资源。 在移动之前，请先查明依赖关系，以便资源在移动后可以继续按预期方式工作。
- **准备移动**：这些是您在移动之前在主要区域中采取的步骤。 例如，可能需要导出 Azure 资源管理器模板，或开始将资源从源复制到目标。
- **移动资源**：如何移动资源取决于资源是什么。 可能需要在目标区域中部署模板，或者将资源故障转移到目标区域。
- **丢弃目标资源**：移动资源后，您可能需要查看目标区域中现在的资源，并决定是否不需要任何资源。
- **提交移动**：在验证目标区域中的资源后，某些资源可能需要最终提交操作。 例如，在现已成为主要区域的目标区域中，可能需要设置灾难恢复到新的次要区域。 
- **清理源**：最后，在新区域中启动并运行所有内容后，可以清理和停用为移动创建的资源以及主区域中的资源。



## <a name="next-steps"></a>后续步骤

有关支持跨区域移动的资源列表，请参阅[资源的移动操作支持](region-move-support.md)。
