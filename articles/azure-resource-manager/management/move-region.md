---
title: 将 Azure 资源移到另一个区域
description: 概述如何在 Azure 区域之间移动 Azure 资源。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: raynew
ms.openlocfilehash: 22d8bcee96b4ac52641d4f0841267195f44fe15a
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485203"
---
# <a name="moving-azure-resources-across-regions"></a>跨区域移动 Azure 资源

本文提供有关在 Azure 区域中移动 Azure 资源的信息。

Azure 地理位置、区域和可用性区域构成了 Azure 全球基础结构的基础。 Azure[地理](https://azure.microsoft.com/global-infrastructure/geographies/)位置通常包含两个或多个[azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 区域是地理位置内的区域，包含可用性区域和多个数据中心。 

在特定 Azure 区域中部署资源后，你可能需要将资源移到不同的区域。

- **与区域发布对齐**：将资源移到之前未提供的新引入的 Azure 区域。
- **服务/功能的对齐**方式：移动资源以利用特定区域中提供的服务或功能。
- **应对业务发展**：将资源移到某个区域，以响应业务变化，例如合并或收购。
- **对齐**：将资源移到业务的本地区域。
- **满足数据需求**：移动资源，以便符合数据驻留要求或数据分类需求。 [了解详细信息](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **响应部署要求**：移动错误部署的资源，或移动以响应容量需求。 
- **应对退役**：移动资源，因为区域已取消。

## <a name="move-process"></a>移动进程

实际移动过程取决于要移动的资源。 但是，有一些常见的关键步骤：

- **验证先决条件**：先决条件包括确保你需要的资源在目标区域中可用，检查是否有足够的配额，并验证你的订阅是否可以访问目标区域。
- **分析依赖关系**：资源可能与其他资源有依赖关系。 在移动之前，请先了解依赖关系，以便移动的资源在移动后可以继续按预期方式工作。
- **准备移动**：在移动之前的主要区域中执行的步骤。 例如，你可能需要导出 Azure 资源管理器模板，或开始将资源从源复制到目标。
- **移动资源**：移动资源的方式取决于它们的内容。 你可能需要在目标区域中部署模板，或将资源故障转移到目标区域。
- **丢弃目标资源**：移动资源后，你可能需要查看目标区域中的资源，并决定是否存在不需要的任何内容。
- **提交移动**：验证目标区域中的资源之后，某些资源可能需要最终提交操作。 例如，在现在是主要区域的目标区域中，可能需要设置新的次要区域的灾难恢复。 
- **清理源**：最后，在新区域中的所有内容都启动并运行后，你可以清理和取消创建用于移动的资源以及主要区域中的资源。



## <a name="next-steps"></a>后续步骤

有关支持跨区域移动的资源的列表，请参阅[移动操作对资源的支持](region-move-support.md)。
