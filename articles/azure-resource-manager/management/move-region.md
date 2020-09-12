---
title: 将 Azure 资源移到另一区域
description: 概述如何跨 Azure 区域移动 Azure 资源。
author: rayne-wiselman
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 09/10/2020
ms.author: raynew
ms.openlocfilehash: 7a71502ec361004079e0962d8bc6433316a4ba81
ms.sourcegitcommit: 3c66bfd9c36cd204c299ed43b67de0ec08a7b968
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/10/2020
ms.locfileid: "90007632"
---
# <a name="moving-azure-resources-across-regions"></a>跨区域移动 Azure 资源

本文提供有关跨 Azure 区域移动 Azure 资源的信息。

Azure 地理位置、区域和可用性区域构成了 Azure 全球基础结构的基础。 Azure [地理位置](https://azure.microsoft.com/global-infrastructure/geographies/)通常包含两个或更多个 [Azure 区域](https://azure.microsoft.com/global-infrastructure/regions/)。 区域是地理位置内的区域，包含可用性区域和多个数据中心。 

在特定的 Azure 区域中部署资源后，你可能会出于以下多种原因，需要将资源移到其他区域。

- **与区域的推出相适应**：将资源移到新推出的 Azure 区域（以前不存在此区域）。
- **与服务/功能相适应**：移动资源以利用特定区域中提供的服务或功能。
- **应对业务发展**：将资源移到某个区域，以应对企业变化，例如合并或收购。
- **对齐**：将资源移到业务的本地区域。
- **满足数据需求**：移动资源，以便符合数据驻留要求或数据分类需求。 [了解详细信息](https://azure.microsoft.com/mediahandler/files/resourcefiles/achieving-compliant-data-residency-and-security-with-azure/Achieving_Compliant_Data_Residency_and_Security_with_Azure.pdf)。
- **响应部署要求**：移动错误部署的资源，或移动以响应容量需求。 
- **应对退役**：移动资源，因为区域已取消。

## <a name="move-resources-with-resource-mover"></a>通过资源移动器移动资源

可以使用 [Azure 资源移动器](../../resource-mover/overview.md)将资源移到不同的区域。 资源移动器提供：

- 跨区域移动资源的单一集线器。
- 缩短了移动时间和复杂性。 你需要的所有内容都在一个位置。
- 移动不同类型的 Azure 资源的简单、一致的体验。
- 标识要移动的资源之间的依赖关系的一种简单方法。 这可以帮助你将相关资源一起移到一起，以便在移动后在目标区域中按预期方式工作。
- 如果要在移动后删除资源，请自动清除源区域中的资源。
- 测试。 如果不想进行完整移动，可以尝试移动，然后将其丢弃。

可以使用几种不同的方法将资源移到另一个区域：

- **开始从资源组移动资源**：通过此方法，你可以从资源组中移动区域。 选择要移动的资源后，该进程会在资源移动器中心继续，以检查资源依赖关系，并协调移动过程。 [了解详细信息](../../resource-mover/move-region-within-resource-group.md)。
- **直接从资源移动器中心开始移动资源**：通过此方法，你可以直接在中心内启动区域移动过程。 [了解详细信息](../../resource-mover/tutorial-move-region-virtual-machines.md)。


## <a name="support-for-region-move"></a>支持区域移动

目前可以使用资源移动器将这些资源移到另一个区域：

- Azure Vm 和关联的磁盘
- NIC
- 可用性集
- Azure 虚拟网络
- 公共 IP 地址
- 网络安全组 (NSG)
- 内部和公共负载均衡器
- Azure SQL 数据库和弹性池

## <a name="region-move-process"></a>区域移动过程

跨区域移动资源的实际过程取决于要移动的资源。 但是，有一些通用的关键步骤：

1. **验证先决条件**：先决条件包括确保你需要的资源在目标区域中可用，检查是否有足够的配额，并验证你的订阅是否可以访问目标区域。
2. **分析依赖关系**：资源可能与其他资源有依赖关系。 在移动之前，请先查明依赖关系，以便资源在移动后可以继续按预期方式工作。
3. **准备移动**：在移动之前的主要区域中执行的步骤。 例如，可能需要导出 Azure 资源管理器模板，或开始将资源从源复制到目标。
4. **移动资源**：移动资源的方式取决于它们的内容。 可能需要在目标区域中部署模板，或者将资源故障转移到目标区域。
5. **丢弃目标资源**：移动资源后，你可能需要查看目标区域中的资源，并决定是否存在不需要的任何内容。
6. **提交移动**：验证目标区域中的资源之后，某些资源可能需要最终提交操作。 例如，在现已成为主要区域的目标区域中，可能需要设置灾难恢复到新的次要区域。 
7. **清理源**：最后，在新区域中的所有内容都启动并运行后，你可以清理和取消创建用于移动的资源以及主要区域中的资源。



## <a name="next-steps"></a>后续步骤

[详细了解](../../resource-mover/about-move-process.md) 资源移动器中的移动进程。
