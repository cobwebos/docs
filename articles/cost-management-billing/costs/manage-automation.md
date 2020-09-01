---
title: 通过自动化功能管理 Azure 成本
description: 本文介绍如何通过自动化功能管理 Azure 成本。
author: bandersmsft
ms.author: banders
ms.date: 08/19/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: a5ab84794884cc0c87bd766be7a0fa2fe4c52aa9
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684399"
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

可以定期从成本管理进行导出，导出大量数据。 建议通过导出的方式来检索未聚合的成本数据。 特别是当使用情况文件太大而无法使用“使用情况详细信息 API”可靠地调用和下载时。 导出的数据置于你所选择的 Azure 存储帐户中。 因此，可以将其加载到你自己的系统中，并按需对其进行分析。 若要在 Azure 门户中配置导出，请参阅[导出数据](tutorial-export-acm-data.md)。

如果要在不同范围自动执行导出，可以首先了解下一部分的示例 API 请求。 可以使用导出 API 创建自动导出，作为常规环境配置的一部分。 自动导出有助于确保你拥有所需的数据。 扩展 Azure 用途时，可以在自己组织的系统中使用。

### <a name="common-export-configurations"></a>常用导出配置

在创建第一个导出之前，请考虑你的方案和启用该方案所需的配置选项。 请考虑以下导出选项：

- **定期** - 确定导出作业的运行频率以及何时将文件放入 Azure 存储帐户。 你可以选择“每天”、“每周”和“每月”。 尝试配置定期，使其与组织内部系统使用的数据导入作业匹配。
- **定期间隔** - 确定导出有效期。 文件仅在定期间隔内导出。
- **时间范围** - 确定在给定运行中由导出生成的数据量。 常用选项是 MonthToDate 和 WeekToDate。
- **StartDate** - 配置何时开始导出计划。 将在 StartDate 上创建一个导出，然后根据周期创建导出。
- **类型** - 导出类型分为以下三种：
  - ActualCost - 显示指定时间段内的总使用量和费用，这些总使用量和费用已累积显示在帐单上。
  - AmortizedCost - 显示指定时间段内的总使用量和成本，并均摊适用的预留购买成本。
  - 用法 - 2020 年 7 月 20 日之前创建的所有导出均为“用法”类型。 将所有计划的导出更新为 ActualCost 或 AmortizedCost。
- **列** - 定义要包含在导出文件中的数据字段。 它们与“用法详细信息 API”中可用的字段相对应。 有关详细信息，请参阅[使用情况详细信息 API](/rest/api/consumption/usagedetails/list)。

### <a name="create-a-daily-month-to-date-export-for-a-subscription"></a>每天为订阅创建本月至今导出

请求 URL：`PUT https://management.azure.com/{scope}/providers/Microsoft.CostManagement/exports/{exportName}?api-version=2020-06-01`

```json
{
  "properties": {
    "schedule": {
      "status": "Active",
      "recurrence": "Daily",
      "recurrencePeriod": {
        "from": "2020-06-01T00:00:00Z",
        "to": "2020-10-31T00:00:00Z"
      }
    },
    "format": "Csv",
    "deliveryInfo": {
      "destination": {
        "resourceId": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/MYDEVTESTRG/providers/Microsoft.Storage/storageAccounts/{yourStorageAccount} ",
        "container": "{yourContainer}",
        "rootFolderPath": "{yourDirectory}"
      }
    },
    "definition": {
      "type": "ActualCost",
      "timeframe": "MonthToDate",
      "dataSet": {
        "granularity": "Daily",
        "configuration": {
          "columns": [
            "Date",
            "MeterId",
            "ResourceId",
            "ResourceLocation",
            "Quantity"
          ]
        }
      }
    }
}
```

### <a name="automate-alerts-and-actions-with-budgets"></a>通过预算自动执行警报和操作

可通过以下两个组件最大化云投资价值。 一个是自动创建预算。 另一个是配置基于成本的业务流程，以应对预算警报。 可以通过多种方法自动执行 Azure 预算创建。 超过配置的警报阈值时，会发生各种警报响应。

以下各节介绍了可用选项，并提供了示例 API 请求，以帮助你开始使用预算自动化。

#### <a name="how-costs-are-evaluated-against-your-budget-threshold"></a>如何根据预算限额计算成本

每天根据预算限额计算一次成本。 创建新预算或在预算重置日时，与限额相比的成本为零/Null，因为可能没有进行计算。

当 Azure 检测到你的成本已超过阈值时，系统将在检测期间的一小时内触发通知。

#### <a name="view-your-current-cost"></a>查看当前成本

若要查看当前成本，需要使用[查询 API](/rest/api/cost-management/query) 进行 GET 调用。

对预算 API 的 GET 调用不返回“成本分析”中显示的当前成本。 相反，此调用返回上次计算的成本。

### <a name="automate-budget-creation"></a>自动创建预算

可以使用[预算 API](/rest/api/consumption/budgets) 自动创建预算。 你也可以使用[预算模板](quick-create-budget-template.md)创建预算。 模板是使 Azure 部署标准化同时确保适当配置和强制实施成本控制的简单方法。

#### <a name="common-budgets-api-configurations"></a>常见预算 API 配置

可以通过多种方法在 Azure 环境中配置预算。 首先考虑方案，然后确定启用该方案的配置选项。 查看以下选项：

- **时间粒度** - 表示预算用于累积和计算成本的重复周期。 最常用的选项是“每月”、“每季度”和“每年”。
- **时间段** - 表示预算有效期。 仅当预算仍然有效时，系统才会主动监控预算并向你发出通知。
- **通知**
  - 联系电子邮件 - 当预算累积成本且超过定义的阈值时，电子邮件地址会收到警报。
  - 联系角色 - 在给定范围内具有匹配的 Azure RBAC 角色的所有用户都会收到带有此选项的电子邮件警报。 例如，订阅所有者可能会收到在订阅范围内创建的预算警报。
  - 联系人组 - 超出警报阈值时，预算将调用已配置的操作组。
- **成本维度筛选器** - 可以通过成本分析或查询 API 进行的筛选，也可以对预算进行。 使用此筛选器，可以缩小对于预算要监视的成本范围。

确定满足你需求的预算创建选项后，请使用 API 创建预算。 以下示例可帮助你开始使用常见预算配置。

**创建筛选到多种资源和标记的预算**

请求 URL：`PUT https://management.azure.com/subscriptions/{SubscriptionId} /providers/Microsoft.Consumption/budgets/{BudgetName}/?api-version=2019-10-01`

```json
{
  "eTag": "\"1d34d016a593709\"",
  "properties": {
    "category": "Cost",
    "amount": 100.65,
    "timeGrain": "Monthly",
    "timePeriod": {
      "startDate": "2017-10-01T00:00:00Z",
      "endDate": "2018-10-31T00:00:00Z"
    },
    "filter": {
      "and": [
        {
          "dimensions": {
            "name": "ResourceId",
            "operator": "In",
            "values": [
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}",
              "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{meterName}"
            ]
          }
        },
        {
          "tags": {
            "name": "category",
            "operator": "In",
            "values": [
              "Dev",
              "Prod"
            ]
          }
        },
        {
          "tags": {
            "name": "department",
            "operator": "In",
            "values": [
              "engineering",
              "sales"
            ]
          }
        }
      ]
    },
    "notifications": {
      "Actual_GreaterThan_80_Percent": {
        "enabled": true,
        "operator": "GreaterThan",
        "threshold": 80,
        "contactEmails": [
          "user1@contoso.com",
          "user2@contoso.com"
        ],
        "contactRoles": [
          "Contributor",
          "Reader"
        ],
        "contactGroups": [
          "/subscriptions/{subscriptionID}/resourceGroups/{resourceGroupName}/providers/microsoft.insights/actionGroups/{actionGroupName}
        ],
        "thresholdType": "Actual"
      }
    }
  }
}
```

### <a name="configure-cost-based-orchestration-for-budget-alerts"></a>为预算警报配置基于成本的业务流程

可以配置预算以使用 Azure 操作组启动自动执行操作。 若要详细了解使用预算自动执行操作，请参阅[使用 Azure 预算自动化](../manage/cost-management-budget-scenario.md)。

## <a name="data-latency-and-rate-limits"></a>数据延迟和速率限制

我们建议每天调用 API 的次数不超过一次。 从 Azure 资源提供程序接收新的使用情况数据时，每隔 4 小时刷新一次成本管理数据。 更频繁地调用不会提供任何其他数据。 相反，它会创建增加的负载。 若要详细了解数据更改的频率和数据延迟的处理方式的详细信息，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。

### <a name="error-code-429---call-count-has-exceeded-rate-limits"></a>错误代码 429 - 调用计数超出了速率限制

若要为所有成本管理订户实现一致的体验，成本管理 API 会受到速率限制。 达到限制时，会收到 HTTP 状态代码 `429: Too many requests`。 API 的当前吞吐量限制如下：

- 每分钟调用 30 次 - 按范围、按用户或按应用程序完成此操作。
- 每分钟调用 200 次 - 按租户、按用户或按应用程序完成此操作。

## <a name="next-steps"></a>后续步骤

- [使用 Power BI 模板应用分析 Azure 成本](https://docs.microsoft.com/azure/cost-management-billing/costs/analyze-cost-data-azure-cost-management-power-bi-template-app)。
- 使用导出功能[创建和管理导出的数据](https://docs.microsoft.com/azure/cost-management-billing/costs/tutorial-export-acm-data)。
- 了解[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usageDetails) 的详细信息。