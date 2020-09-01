---
title: Azure 安全中心的资产清单
description: 了解 Azure 安全中心的资产管理经验，提供对所有安全中心监视的资源的完整可见性。
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 08/11/2020
ms.service: security-center
ms.topic: conceptual
ms.openlocfilehash: f3a542cd62c3d593dbc0cce7982d47222e9a7c88
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/31/2020
ms.locfileid: "89181097"
---
# <a name="explore-and-manage-your-resources-with-asset-inventory-and-management-tools"></a>利用资产清单和管理工具浏览和管理资源

Azure 安全中心的 "资产清单" 页提供了一个页面，用于查看已连接到安全中心的资源的安全状况。 

安全中心会定期分析 Azure 资源的安全状态，以识别潜在的安全漏洞。 然后，提供有关如何修正这些漏洞的建议。

当任何资源具有未完成的建议时，它们将显示在清单中。

使用此视图及其筛选器来解决此类问题：

- 哪些标准层订阅中有未完成的建议？
- 标记为 "生产" 的计算机中哪些计算机缺少 Log Analytics 代理？
- 标记有特定标记的计算机有多少具有未完成的建议？
- 特定资源组中有多少个资源从漏洞评估服务获得了安全发现？

此工具的资产管理可能是巨大的，并会持续增长。 


## <a name="availability"></a>可用性

|方面|详细信息|
|----|:----|
|发布状态：|预览|
|计价|Free|
|必需的角色和权限：|所有用户|
|云：|![是](./media/icons/yes-icon.png) 商业云<br>![否](./media/icons/no-icon.png) 国家/主权 (US Gov、中国 Gov、其他 Gov) |
|||


## <a name="what-are-the-key-features-of-asset-inventory"></a>资产清单的主要功能有哪些？

"清单" 页提供了以下工具：

- **摘要** -在定义任何筛选器之前，清单视图顶部的一个突出显示的值块显示：

    - **资源总数**：连接到安全中心的资源总数。
    - 不**正常资源**：具有有效安全建议的资源。 [详细了解安全建议](https://docs.microsoft.com/azure/security-center/security-center-recommendations)。
    - 未**监视的资源**：具有代理监视问题的资源-已部署 Log Analytics 代理，但代理未发送数据或存在其他运行状况问题。

- **筛选器** -页面顶部的多个筛选器提供了一种方法，可根据要尝试回答的问题快速优化资源列表。 例如，如果你想要回答*标记为 "生产" 的计算机中缺少 Log Analytics 代理*的问题，则**可以将****代理监视**筛选器与 tag 筛选器组合起来，如下面的代码片段所示：

    ![筛选到未监视的生产资源](./media/asset-inventory/filtering-to-prod-unmonitored.gif)

    应用筛选器后，汇总值将更新以与查询结果相关。 

- **导出选项** -库存提供将所选筛选器选项的结果导出到 CSV 文件的选项。 此外，还可以将查询本身导出到 Azure 资源 Graph 资源管理器，进一步优化、保存或修改 KQL 查询。

    ![清单的导出选项](./media/asset-inventory/inventory-export-options.png)

    > [!TIP]
    > KQL 文档提供了一个数据库，其中包含一些示例数据以及一些简单的查询，以获取语言的 "感觉"。 有关[详细信息，请参阅此 KQL 教程](https://docs.microsoft.com/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer)。

- **资产管理选项** -库存使你可以执行复杂的发现查询。 如果找到了与查询匹配的资源，则清单将提供操作的快捷方式，例如：

    - 将标记分配给筛选的资源-选择要标记的资源旁的复选框
    - 将新服务器加入安全中心-使用 " **添加非 Azure 服务器** " 工具栏按钮


## <a name="how-does-asset-inventory-work"></a>资产清单如何工作？

资产清单利用 [Azure 资源关系图 (ARG) ](https://docs.microsoft.com/azure/governance/resource-graph/)，这是一项 azure 服务，可在多个订阅中查询安全中心的安全状态数据。

ARG 旨在提供高效的资源浏览功能，使其能够大规模查询。

使用 [Kusto 查询语言 (KQL) ](https://docs.microsoft.com/azure/data-explorer/kusto/query/)，资产清单可以通过交叉引用使用其他资源属性的 ASC 数据快速生成深度见解。


## <a name="how-to-use-asset-inventory"></a>如何使用资产清单

1. 从安全中心的边栏中选择 " **清点**"。

1. （可选）若要显示特定资源，请在 " **按名称筛选** " 框中输入名称。

1. 在筛选器中选择相关选项以创建要执行的特定查询。

    ![库存筛选器](./media/asset-inventory/inventory-filters.png)

    默认情况下，资源按活动安全建议的数量进行排序。

    > [!IMPORTANT]
    > 每个筛选器中的选项特定于当前所选订阅中的资源 **和** 其他筛选器中的选择。
    >
    > 例如，如果你只选择了一个订阅，并且该订阅没有资源具有未完成的安全建议来修正 (0 个不正常的资源) ，则 **建议** 筛选器将没有任何选项。 

1. 若要使用 " **安全结果" 包含** 筛选器，请输入从 "ID"、"安全检查" 或 "CVE" 名称中查找 "筛选器"，以筛选受影响的资源：

    !["安全发现包含" 筛选器](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > **安全发现包含**和**标记**筛选器仅接受单个值。 若要按多个筛选，请使用 " **添加筛选器**"。

1. 若要使用 " **定价层** " 筛选器，请选择一个或多个选项 (可用、部分或标准) ：

    - **免费** -免费定价层上的资源
    - **标准** -标准定价层上的资源
    - **部分** -这适用于标准定价层上的订阅，但禁用了某些可选的安全计划。 例如，以下订阅在标准层上，但禁用了标准层的五个元素。 

        ![标准 (分部) 定价层上的订阅](./media/asset-inventory/pricing-tier-partial.png)

1. 若要进一步检查查询的结果，请选择你感兴趣的资源。

1. 还可以选择 " **资源图资源管理器" 中** 的 "查看"，以在资源图形资源管理器中打开查询

    ![ARG 中的清单查询](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. 如果已定义了一些筛选器并使页面保持打开状态，则安全中心不会自动更新结果。 除非手动重新加载页面或选择 **刷新**，否则对资源所做的任何更改都不会影响显示的结果。


## <a name="faq---inventory"></a>FAQ-库存

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>为什么所有订阅、计算机、存储帐户等都未显示？

清单视图列出了云安全状况管理的资源 (CSPM) 透视。 筛选器不返回环境中的每个资源;只有具有未处理 (或 "活动" 的文件 ) 建议。 

例如，如果你有九个订阅，但目前只有八个建议，则当你按 **资源类型 = 订阅** 进行筛选时，你只会看到具有活动建议的八个订阅：

![如果没有活动的建议，则不返回所有的 sub](./media/asset-inventory/filtered-subscriptions-some.png)


### <a name="why-do-some-of-my-resources-show-blank-values-in-the-pricing-or-agent-monitoring-columns"></a>为什么某些资源在 "定价" 或 "代理" 监视列中显示空值？

并非所有安全中心监视的资源都有代理。 例如，Azure 存储帐户或 PaaS 资源（例如磁盘、逻辑应用、Data Lake 分析和事件中心）。

当定价或代理监视与资源无关时，清单列中将不会显示任何内容。

![某些资源在代理监视或定价列中显示空白信息](./media/asset-inventory/agent-pricing-blanks.png)



## <a name="next-steps"></a>后续步骤

本文介绍了 Azure 安全中心的 "资产清单" 页。

有关相关工具的详细信息，请参阅以下页面：

- [Azure 资源关系图 (ARG) ](https://docs.microsoft.com/azure/governance/resource-graph/)

- [Kusto 查询语言 (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)