---
title: 比较 Azure Migrate 和 Site Recovery 迁移到 Azure
description: 总结了使用 Azure Migrate 进行迁移的优点，而不是 Site Recovery。
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: raynew
ms.openlocfilehash: 358efaa1493aa08fb76c9bb83e0e4289950e0969
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/06/2020
ms.locfileid: "87844314"
---
# <a name="migrating-to-azure"></a>迁移到 Azure

对于迁移，我们建议使用 Azure Migrate 服务将 Vm 和服务器迁移到 Azure，而不是迁移到 Azure Site Recovery 服务。 [详细了解](../migrate/migrate-services-overview.md)Azure Migrate。


## <a name="why-use-azure-migrate"></a>为何使用 Azure Migrate？

使用 Azure Migrate 迁移提供了许多优点：
 
 
- Azure Migrate 提供了一个集中式中心，用于发现、评估和迁移到 Azure。
- 使用 Azure Migrate 可通过 Azure Migrate 工具、其他 Azure 服务和第三方工具提供互操作性和未来的扩展性。
- Azure Migrate：服务器迁移工具专用于将服务器迁移到 Azure。 它针对迁移进行了优化。 不需要了解与迁移无关的概念和方案。 
- 从 VM 开始复制开始，180天内没有用于迁移的工具使用量。 这为你提供了完成迁移的时间。 只需为复制中使用的存储和网络资源付费，并为测试迁移期间使用的计算费用付费。
- Azure Migrate 支持 Site Recovery 支持的所有迁移方案。 此外，对于 VMware Vm，Azure Migrate 提供无代理迁移选项。
- 我们仅确定 Azure Migrate： Server 迁移工具的新迁移功能的优先级。 这些功能并不针对 Site Recovery。

## <a name="when-to-use-site-recovery"></a>何时使用 Site Recovery？

应使用 Site Recovery：

- 用于本地计算机到 Azure 的灾难恢复。
- 对于 azure Vm，azure 区域之间的灾难恢复。

尽管我们建议使用 Azure Migrate 将本地服务器迁移到 Azure，但如果已使用 Site Recovery 启动迁移旅程，可以继续使用它来完成迁移。  

## <a name="next-steps"></a>后续步骤

> 查看有关 Azure Migrate 的[常见问题解答](../migrate/resources-faq.md)。
