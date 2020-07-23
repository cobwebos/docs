---
title: 通过自动化功能管理 Azure 成本
description: 本文介绍如何通过自动化功能管理 Azure 成本。
author: bandersmsft
ms.author: banders
ms.date: 04/15/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: adwise
ms.openlocfilehash: 0727f98b917944f3721c6c6758fde05c2efd8773
ms.sourcegitcommit: 0a5bb9622ee6a20d96db07cc6dd45d8e23d5554a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84449825"
---
# <a name="manage-costs-with-automation"></a>通过自动化功能管理成本

可以使用成本管理自动化生成一组自定义解决方案来检索和管理成本数据。 本文介绍成本管理自动化的常见方案以及根据你的情况提供的选项。 如果要使用 API 进行开发，则提供了常见的 API 请求示例来帮助加速开发过程。

## <a name="automate-cost-data-retrieval-for-offline-analysis"></a>实现成本数据检索的自动化以进行脱机分析

可能需要下载 Azure 成本数据以将其与其他数据集合并。 或者，可能需要将成本数据集成到你自己的系统中。 根据所涉及的数据量，可以使用不同的选项。 在任何情况下，都必须在适当范围内拥有成本管理权限，才能使用 API 和工具。 有关详细信息，请参阅[向数据分配访问权限](https://docs.microsoft.com/azure/cost-management-billing/costs/assign-access-acm-data)。

## <a name="suggestions-for-handling-large-datasets"></a>处理大型数据集的建议

如果你的组织在多个资源或订阅中具有大型 Azure 状态，那么你将拥有大量使用情况详细信息数据。 Excel 通常无法加载此类大型文件。 在这种情况下，我们建议使用以下选项：

**Power BI**

Power BI 用于引入和处理大量数据。 如果你是企业协议客户，则可以使用 Power BI 模板应用来分析计费帐户的成本。 报表包含客户使用的重要视图。 有关详细信息，请参阅[使用 Power BI 模板应用分析 Azure 成本](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。

**Power BI 数据连接器**

如果要每天分析数据，我们建议使用 [Power BI 数据连接器](https://docs.microsoft.com/power-bi/connect-data/desktop-connect-azure-cost-management)获取详细分析的数据。 该连接器会将你创建的任何报表都保持为最新状态，这会产生更多的费用。

**成本管理导出**

可能不需要每天分析数据。 如果是这样，请考虑使用成本管理的[导出](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)功能，计划将数据导出到 Azure 存储帐户。 然后，可以根据需要将数据加载到 Power BI 中，如果文件足够小，则在 Excel 中进行分析。 Azure 门户中提供了导出功能，或者可以使用[导出 API](https://docs.microsoft.com/rest/api/cost-management/exports) 配置导出。

**使用情况详细信息 API**

如果有少量成本数据集，请考虑使用[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usageDetails)。 如果有大量成本数据，则应在一段时间内请求尽可能少的使用情况数据。 为此，请指定一个较小的时间范围或在请求中使用筛选器。 例如，在需要三年成本数据的情况下，如果对不同的时间范围进行多次调用，而不是一次调用，则 API 会做得更好。 因此，可以将数据加载到 Excel 中进行进一步分析。

## <a name="automate-retrieval-with-usage-details-api"></a>使用使用情况详细信息 API 实现检索的自动化

借助[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usageDetails)，可以轻松地获取与 Azure 帐单相对应的原始、未聚合的成本数据。 当你的组织需要编程数据检索解决方案时，API 非常有用。 如果要分析较小的成本数据集，请考虑使用 API。 但是，如果你有更大的数据集，则应使用以前确定的其他解决方案。 使用情况详细信息中的数据每天按计量提供。 计算每月帐单时使用此方法。 这些 API 的正式发布版 (GA) 是 `2019-10-01`。 使用 `2019-04-01-preview` 通过 API 访问预留和 Azure 市场购买的预览版本。

### <a name="usage-details-api-suggestions"></a>使用情况详细信息 API 建议

**请求计划**

我们建议每天向使用情况详细信息 API 发出的请求不超过一个。 有关成本数据的刷新频率以及如何处理舍入的详细信息，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data#rated-usage-data-refresh-schedule)。

**无筛选的目标顶级范围**

使用 API 在可用的最高级别范围内获取所需的所有数据。 请等到引入所有所需数据，再执行任何筛选、分组或聚合分析。 API 专门进行了优化，以提供大量未聚合的原始成本数据。 若要详细了解成本管理中提供的范围，请参阅[了解并使用范围](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-work-scopes)。 下载某个范围的所需数据后，便可使用 Excel 通过筛选器和透视表进一步分析数据。

## <a name="example-usage-details-api-requests"></a>示例使用情况详细信息 API 请求

Microsoft 客户使用以下示例请求来解决你可能遇到的常见情况。

### <a name="get-usage-details-for-a-scope-during-specific-date-range"></a>获取特定日期范围内范围的使用情况详细信息

请求返回的数据对应于计费系统收到使用情况的日期。 其中可能包含多张发票的费用。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?$filter=properties%2FusageStart%20ge%20'2020-02-01'%20and%20properties%2FusageEnd%20le%20'2020-02-29'&$top=1000&api-version=2019-10-01

```

### <a name="get-amortized-cost-details"></a>获取摊销成本详细信息

如果需要实际成本，以便将购买显示为应记项，则请在以下请求中将 metric 更改为 `ActualCost`。 若要使用摊销成本和实际成本，则必须使用 `2019-04-01-preview` 版本。 当前 API 版本的工作方式与 `2019-10-01` 版本相同，新的 type/metric 特性和已更改的属性名称除外。 如果你有 Microsoft 客户协议，则筛选器在以下示例中为 `startDate` 和 `endDate`。

```http
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

## <a name="retrieve-large-cost-datasets-recurringly-with-exports"></a>使用导出功能定期检索大型成本数据集

导出功能是用于定期计划成本数据转储的解决方案。 对于使用情况文件可能过大而无法采用使用情况详细信息 API 可靠地调用和下载数据的组织，建议检索未聚合的成本数据。 数据放置在所选的 Azure 存储帐户中。 因此，可以将其加载到你自己的系统中，并根据团队的需要对其进行分析。 若要在 Azure 门户中配置导出，请参阅[导出数据](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。

## <a name="data-latency-and-rate-limits"></a>数据延迟和速率限制

我们建议每天调用 API 的次数不超过一次。 从 Azure 资源提供程序接收新的使用情况数据时，每隔 4 小时刷新一次成本管理数据。 更频繁地调用不会提供任何其他数据。 相反，它会创建增加的负载。 若要详细了解数据更改的频率和数据延迟的处理方式的详细信息，请参阅[了解成本管理数据](https://docs.microsoft.com/azure/cost-management-billing/costs/understand-cost-mgt-data)。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>错误代码 429 - 调用计数超出了速率限制

若要为所有成本管理订户实现一致的体验，成本管理 API 会受到速率限制。 达到限制时，会收到 HTTP 状态代码 `429: Too many requests`。 API 的当前吞吐量限制如下：

- 每分钟调用 30 次 - 按范围、按用户或按应用程序完成此操作。
- 每分钟调用 200 次 - 按租户、按用户或按应用程序完成此操作。

## <a name="next-steps"></a>后续步骤

- [使用 Power BI 模板应用分析 Azure 成本](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。
- 使用导出功能[创建和管理导出的数据](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。
- 了解[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usageDetails) 的详细信息。