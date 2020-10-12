---
title: Azure Resource Graph 中的顾问数据
description: 对 Azure Resource Graph 中的顾问数据进行查询
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: 66bb500d419d1f5537afafd7a2df543ded8cc7ce
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "87057775"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>在 Resource Graph 资源管理器 (Azure Resource Graph) 中查询顾问数据

顾问资源现已加入到 [Azure Resource Graph](https://azure.microsoft.com/features/resource-graph/)。 这为顾问建议的许多大规模客户方案奠定了基础。 以前无法大规模实现但现在可以使用 Resource Graph 来实现的少数方案如下：
* 提供在 Azure 门户中对你的所有订阅执行复杂查询的功能
* 按类别类型（如可靠性、性能）和影响类型（高、中、低）汇总的建议
* 特定建议类型的所有建议
* 按建议类别列出的受影响资源计数

![Azure Resource Graph 资源管理器中的顾问](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph 中的顾问资源类型

[Resource Graph](../governance/resource-graph/index.yml) 中的可用顾问资源类型：在顾问资源下有 3 种资源类型可供查询。 下面是现在可在 Resource Graph 中查询的资源列表。
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

这些资源类型列在名为“AdvisorResources”的新表下，你也可以在 Azure 门户的 Resource Graph 资源管理器中查询该表。


## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问可靠性建议](advisor-high-availability-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问卓越运营建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](/rest/api/advisor/)
