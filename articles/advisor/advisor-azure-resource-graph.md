---
title: Azure 资源图中的顾问数据
description: 在 Azure 资源图中对 Advisor 数据进行查询
ms.topic: article
ms.date: 03/12/2020
ms.author: sagupt
ms.openlocfilehash: f8ad8fd450bc004d9caa2699922717f38d38b482
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/28/2020
ms.locfileid: "79502446"
---
# <a name="query-for-advisor-data-in-resource-graph-explorer-azure-resource-graph"></a>在资源 Graph 资源管理器中查询 Advisor 数据（Azure 资源图）

Advisor 资源现在载入到[Azure 资源关系图](https://azure.microsoft.com/features/resource-graph/)。 这为顾问建议的多个大规模客户方案奠定了基础。 在规模达到之前，几乎不可能发生的情况，现在可以使用资源图实现此目的：
* 提供在 Azure 门户中为所有订阅执行复杂查询的功能
* 按类别类型（如高可用性、性能）和影响类型（高、中、低）汇总的建议
* 针对特定建议类型的所有建议
* 受建议类别影响的资源计数

![Azure 资源图资源管理器中的顾问](./media/azure-resource-graph-1.png)  


## <a name="advisor-resource-types-in-azure-graph"></a>Azure Graph 中的顾问资源类型

[资源图](https://docs.microsoft.com/azure/governance/resource-graph/)中的可用顾问资源类型：有3种资源类型可用于在 Advisor 资源下查询。 下面是现在可在资源关系图中进行查询的资源列表。
* Microsoft.Advisor/configurations
* Microsoft.Advisor/recommendations
* Microsoft.Advisor/suppressions

这些资源类型列在名为 AdvisorResources 的新表下，您还可以在 Azure 门户的资源 Graph 资源管理器中进行查询。


## <a name="next-steps"></a>后续步骤

有关顾问建议的详细信息，请参阅以下资源：
* [Azure 顾问简介](advisor-overview.md)
* [顾问入门](advisor-get-started.md)
* [顾问成本建议](advisor-cost-recommendations.md)
* [顾问性能建议](advisor-performance-recommendations.md)
* [顾问安全性建议](advisor-security-recommendations.md)
* [顾问操作优秀建议](advisor-operational-excellence-recommendations.md)
* [顾问 REST API](https://docs.microsoft.com/rest/api/advisor/)
