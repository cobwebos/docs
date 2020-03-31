---
title: Azure 资源图中的顾问数据
description: 在 Azure 资源图中查询顾问数据
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502446"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>查询资源图资源管理器中的顾问数据（Azure 资源图）

顾问资源现在已连接到 Azure[资源图](https://azure.microsoft.com/features/resource-graph/)。 这为顾问建议的许多大规模客户方案奠定了基础。 以前无法大规模执行且现在可以使用资源图实现的方案很少是：
* 提供对 Azure 门户中的所有订阅执行复杂查询的功能
* 按类别类型（如高可用性、性能）和影响类型（高、中、低）汇总的建议
* 特定建议类型的所有建议
* 按建议类别影响的资源计数

![Azure 资源图资源管理器中的顾问](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure 图形中的顾问资源类型

[资源图](https://docs.microsoft.com/azure/governance/resource-graph/)中的可用顾问资源类型：有 3 种资源类型可用于在顾问资源下查询。 下面是资源图中现在可用于查询的资源的列表。
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

这些资源类型列在名为 AdvisorResources 的新表下，您还可以在 Azure 门户中的资源图资源管理器中查询该表。


## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](https://docs.microsoft.com/rest/api/advisor/)
