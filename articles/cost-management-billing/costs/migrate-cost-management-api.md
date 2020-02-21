---
title: 将 EA 迁移到 Microsoft 客户协议 API - Azure
description: 本文帮助你了解将 Microsoft 企业协议 (EA) 迁移到 Microsoft 客户协议的影响。
author: bandersmsft
ms.author: banders
ms.date: 02/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 397e0a21b1ba11b3bdd74c2030ff358c1ce159d8
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201027"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>从企业协议迁移到 Microsoft 客户协议 API

本文帮助你了解企业协议 (EA) 与 Microsoft 客户协议 (MCA) 帐户之间的数据结构、API 和其他系统集成差异。 Azure 成本管理支持这两种帐户类型的 API。 在继续阅读之前，请先查看[设置 Microsoft 客户协议的计费帐户](../manage/mca-setup-account.md)一文。

具有现有 EA 帐户的组织应查看本文，同时设置一个 MCA 帐户。 以前，续订 EA 帐户需要完成少量的工作，以便从旧注册转移到新注册。 但是，迁移到 MCA 帐户需要额外的工作。 之所以需要付出额外的工作，是因为底层计费子系统发生了变化，影响了所有与成本相关的 API 和服务产品。

## <a name="mca-apis-and-integration"></a>MCA API 和集成

使用 MCA API 和新集成可以：

- 通过本机 Azure API 获得 API 完全可用性。
- 在单个计费帐户中配置多个发票。
- 访问合并的 API 以及 Azure 服务使用情况、第三方市场使用情况和市场购买内容。
- 使用 Azure 成本管理查看各个计费配置文件（与注册相同）中的成本。
- 访问新的 API，以显示成本、在成本超出预定义的阈值时获得通知，并自动导出原始数据。

## <a name="migration-checklist"></a>迁移核对清单

以下各项可帮助你过渡到 MCA API。

- 请熟悉新的 [Microsoft 客户协议计费帐户](../understand/mca-overview.md)。
- 确定所用的 API，并查看以下部分中替换了哪些 API。
- 熟悉 [Azure 资源管理器 REST API](/rest/api/azure)。
- 如果你未曾用过 Azure 资源管理器 API，请[将客户端应用注册到 Azure AD](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 更新所有编程代码以[使用 Azure AD 身份验证](/rest/api/azure/#create-the-request)。
- 更新所有编程代码，以将 EA API 调用替换为 MCA API 调用。
- 更新错误处理，以使用新的错误代码。
- 查看其他集成产品/服务，例如 Cloudyn 和 Power BI，以完成其他所需操作。

## <a name="ea-apis-replaced-with-mca-apis"></a>已替换为 MCA API 的 EA API

EA API 使用 API 密钥进行身份验证和授权。 MCA API 使用 Azure AD 身份验证。

| 目的 | EA API | MCA API |
| --- | --- | --- |
| 余额和额度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 使用情况 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用情况 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 市场使用情况 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 计费周期 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 价目表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 预留项购买 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 预留建议 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 预留项使用情况 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> 通过[使用情况详细信息 API](/rest/api/consumption/usagedetails) 获取 Azure 服务和第三方市场使用情况。

以下 API 适用于 MCA 计费帐户：

| 目的 | Microsoft 客户协议 (MCA) API |
| --- | --- |
| 计费帐户<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 计费配置文件<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 发票科目<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 发票 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 计费订阅 | {scope}/billingSubscriptions |

<sup>2</sup> API 返回对象列表，这些对象为范围，Azure 门户中的“成本管理”体验和 API 在其中运行。 有关成本管理范围的详细信息，请参阅[了解和使用范围](understand-work-scopes.md)。

如果使用任何现有的 EA API，则需要将其更新才能支持 MCA 计费帐户。 下表显示了其他集成更改：

| 目的 | 旧产品/服务 | 新产品/服务 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft Consumption Insights](/power-bi/desktop-connect-azure-consumption-insights) 内容包和连接器 |  [Azure Consumption Insights 连接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>用于获取余额和额度的 API

[获取余额摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) API 提供以下信息的每月摘要：

- 余额
- 新购买
- Azure 市场服务费用
- 调整
- 服务超额费用

所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。

获取余额摘要 API 已替换为 billingAccounts/billingProfiles/availableBalance API。

若要使用可用余额 API 获取可用余额：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>用于获取成本和使用情况的 API

使用以下 API 从 Azure 服务使用情况、第三方市场使用情况和其他市场购买内容中获取每日成本细分。 为 Azure 服务和第三方市场使用情况合并了以下单独 API。 旧 API 已替换为 [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API。 它添加了市场购买内容，这些内容过去仅显示在截止当前的余额摘要中。

- [获取使用情况详细信息/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取使用情况详细信息/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取市场应用商店费用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [获取市场应用商店费用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。

上面的所有 API 已替换为消耗/使用情况详细信息 API。

若要通过使用情况详细信息 API 获取使用情况详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

与所有成本管理 API 一样，使用情况详细信息 API 可在多个范围使用。 对于惯常在注册级别接收的开票成本，请使用计费配置文件范围。  有关成本管理范围的详细信息，请参阅[了解和使用范围](understand-work-scopes.md)。

| 类型 | ID 格式 |
| --- | --- |
| 计费帐户 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 计费配置文件 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 订阅 | `/subscriptions/{subscriptionId}` |
| 资源组 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

使用以下 querystring 参数更新所有编程代码。

| 旧参数 | 新参数 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支持 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

响应正文也已更改。

旧响应正文：

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

新响应正文：

```
{
  "value": [{
    "id": "{scope}/providers/Microsoft.Consumption/usageDetails/###",
    "name": "###",
    "type": "Microsoft.Consumption/usageDetails",
    "tags": {...},
    "properties": [{...}, ...],
    "nextLink": "string"
  }, ...]
}
```

包含使用记录数组的属性名称已从 data 更改为 _values_。 每条记录过去往往包含详细属性的扁平列表。 但是，每条记录现在将所有详细信息包含在名为 _properties_ 的嵌套属性中（对于标记除外）。 新结构与其他 Azure API 一致。 某些属性名称已更改。 下表显示了相应的属性。

| 旧属性 | 新属性 | 说明 |
| --- | --- | --- |
| AccountId | 空值 | 不会跟踪订阅创建者。 使用 invoiceSectionId（与 departmentId 相同）。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | 空值 | 不会跟踪订阅创建者。 使用 invoiceSectionName（与 departmentName 相同）。 |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 请注意，这些属性是对立的。 如果 isAzureCreditEnabled 为 true，则 ChargesBilledSeparately 为 false。 |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 确切的字符串值可能不同。 |
| ConsumedServiceId | 无 | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| Date 和 usageStartDate | date | &nbsp;  |
| 日期 | 无 | 分析日期中的“日”。 |
| DepartmentId | invoiceSectionId | 确切的值不同。 |
| DepartmentName | invoiceSectionName | 确切的字符串值可能不同。 如果需要，请配置发票科目以匹配部门。 |
| ExtendedCost 和 Cost | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 是重复性费用 | 无 | &nbsp;  |
| 位置 | location | &nbsp;  |
| MeterCategory | meterCategory | 确切的字符串值可能不同。 |
| 计量 ID | meterId | 确切的字符串值不同。 |
| MeterName | meterName | 确切的字符串值可能不同。 |
| MeterRegion | meterRegion | 确切的字符串值可能不同。 |
| MeterSubCategory | meterSubCategory | 确切的字符串值可能不同。 |
| 月份 | 无 | 分析日期中的“月”。 |
| 产品名称 | 无 | 使用 publisherName 和 productOrderName。 |
| OfferID | 无 | &nbsp;  |
| 订单编号 | 无 | &nbsp;  |
| PartNumber | 无 | 使用 meterId 和 productOrderName 来唯一标识价格。 |
| 计划名称 | productOrderName | &nbsp;  |
| Products | Products |   |
| ProductId | productId | 确切的字符串值不同。 |
| 发布者名称 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 确切的字符串值不同。 |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | 无 | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | 空值 | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 确切的字符串值可能不同。 |
| ServiceTier | meterSubCategory | 确切的字符串值可能不同。 |
| StoreServiceIdentifier | 空值 | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| Tags | 标记 | tags 属性适用于根对象，而不适用于嵌套的 properties 属性。 |
| UnitOfMeasure | unitOfMeasure | 确切的字符串值不同。 |
| usageEndDate | date | &nbsp;  |
| 年龄 | 无 | 分析日期中的“年”。 |
| （新） | billingCurrency | 费用所用的货币。 |
| （新） | billingProfileId | 计费配置文件（与注册相同）的唯一 ID。 |
| （新） | billingProfileName | 计费配置文件（与注册相同）的名称。 |
| （新） | chargeType | 用于区分 Azure 服务使用情况、市场使用情况和购买内容。 |
| （新） | invoiceId | 发票的唯一 ID。 对于当前未结月份为空。 |
| （新） | publisherType | 购买内容的发布者类型。 对于使用情况为空。 |
| （新） | serviceFamily | 购买类型。 对于使用情况为空。 |
| （新） | servicePeriodEndDate | 购买的服务的结束日期。 |
| （新） | servicePeriodStartDate | 购买的服务的开始日期。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>已替换为发票 API 的计费周期 API

MCA 计费帐户不使用计费周期。 它们使用发票将成本范围限定为特定的计费周期。 [计费周期 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 已替换为发票 API。 所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。

若要通过发票 API 获取发票：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>价目表 API

本部分介绍现有的价目表 API，并提供有关转移到 Microsoft 客户协议的价目表 API 的建议。 此外，介绍 Microsoft 客户协议的价目表 API，并解释价目表中的字段。 [Enterprise 获取价目表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)和 [Enterprise 获取计费周期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) API 已替换为 Microsoft 客户协议的价目表 API (Microsoft.Billing/billingAccounts/billingProfiles/pricesheet)。 新 API 支持 JSON 和 CSV 格式，并采用异步 REST 格式。 所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。

### <a name="billing-enterprise-apis"></a>计费企业 API

过去，你会在企业注册中使用计费企业 API 来获取价格和计费周期信息。 身份验证和授权使用 Azure Active Directory Web 令牌。

若要通过价目表和计费周期 API 获取指定企业注册的适用价格：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>Microsoft 客户协议的价目表 API

使用 Microsoft 客户协议的价目表 API 查看所有 Azure 消耗和市场消耗服务的价格。 为计费配置文件显示的价格适用于属于该计费配置文件的所有订阅。

使用价目表 API 查看 CSV 格式的所有 Azure 消耗服务价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

使用价目表 API 查看 JSON 格式的所有 Azure 消耗服务价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用该 API 会返回整个帐户的价目表。 但是，也可以获取价目表的精简版本（PDF 格式）。 摘要包括针对特定发票计费的 Azure 消耗和市场消耗服务。 发票由 {invoiceId} 标识，此 ID 与发票摘要 PDF 文件中显示的“发票编号”相同。  下面是一个示例。

![显示对应于 InvoiceId 的发票编号的示例插图](./media/migrate-cost-management-api/invoicesummary.png)

若要通过价目表 API 查看 CSV 格式的发票信息：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要通过价目表 API 查看 JSON 格式的发票信息：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

还可以查看当前未结计费周期或服务期限内任何 Azure 消耗或市场消耗服务的估算价格。

若要通过价目表 API 查看 CSV 格式的消耗服务估算价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要通过价目表 API 查看 JSON 格式的消耗服务估算价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客户协议价目表 API 是异步 REST API。  这些 API 的响应不同于旧式同步 API。 API 响应正文也已更改。

#### <a name="old-response-body"></a>旧响应正文

下面是同步 REST API 响应的示例：

```
[
        {
            "id": "enrollments/573549891/billingperiods/2016011/products/343/pricesheets",
            "billingPeriodId": "201704",
            "meterId": "dc210ecb-97e8-4522-8134-2385494233c0",
            "meterName": "A1 VM",
            "unitOfMeasure": "100 Hours",
            "includedQuantity": 0,
            "partNumber": "N7H-00015",
            "unitPrice": 0.00,
            "currencyCode": "USD"
        },
        {
    ]
```

#### <a name="new-response-body"></a>新响应正文

这些 API 支持 [Azure REST 异步](../../azure-resource-manager/management/async-operations.md)格式。 使用 GET 调用 API 会收到以下响应：

```
No Response Body

HTTP Status 202 Accepted
```

以下标头连同输出位置一起发送：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

对位置发出另一次 GET 调用。 在操作进入完成或失败状态之前，对 GET 调用的响应是相同的。 完成后，对 GET 调用位置的响应会返回下载 URL。 这就像同时执行了操作一样。 下面是一个示例：

```
HTTP Status 200

{
  "id": "providers/Microsoft.Consumption/operationresults/{operationId}",
  "name": {operationId},
  "type": “Microsoft.Consumption/operationResults",
  "properties" : {
    "downloadUrl": {urltoblob},
    "validTill": "Date"
  }
}
```

客户端还可以对 `Azure-AsyncOperation` 发出 GET 调用。 终结点返回操作的状态。

下表显示了旧式 Enterprise 获取价目表 API 中的字段。 其中包括 Microsoft 客户协议的新价目表中的相应字段：

| 旧属性 | 新属性 | 说明 |
| --- | --- | --- |
| billingPeriodId  | _不适用_ | 不适用。 对于 Microsoft 客户协议，发票和关联的价目表取代了 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 确切的字符串值可能不同。 |
| includedQuantity  | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 |
| partNumber  | _不适用_ | 请改用 productOrderName（与 offerID 相同）和 meterID 的组合。 |
| unitPrice  | unitPrice | 单价适用于在 Microsoft 客户协议中使用的服务。 |
| currencyCode  | pricingCurrency | Microsoft 客户协议使用定价货币和计费货币来表示价格。 currencyCode 对应于 Microsoft 客户协议中的 pricingCurrency。 |
| offerID | productOrderName | 如果不使用 OfferID，可以改用 productOrderName，但两者不同。 但是，productOrderName 和 meter 确定 Microsoft 客户协议中与旧式注册中的 meterId 和 OfferID 相关的定价。 |

## <a name="consumption-price-sheet-api-operations"></a>消耗价目表 API 操作

对于企业协议，过去你会使用消耗价目表 API [获取](/rest/api/consumption/pricesheet/get)和[按计费周期获取](/rest/api/consumption/pricesheet/getbybillingperiod)操作来按 subscriptionId 或计费周期获取范围。 该 API 使用 Azure 资源管理身份验证。

若要通过价目表 API 获取范围的价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

若要通过价目表 API 按计费周期获取价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

如果不使用上述 API 终结点，可以改用 Microsoft 客户协议的以下终结点：

**Microsoft 客户协议的价目表 API（异步 REST API）**

此 API 适用于 Microsoft 客户协议，并提供更多的特性。

**计费帐户中计费配置文件范围的价目表**

此 API 是现有的 API。 它已经过更新，可提供计费帐户中计费配置文件的价目表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>按计费帐户获取范围的价目表

在计费帐户中的注册范围获取价目表时，将使用 Azure 资源管理器身份验证。

若要在计费帐户中获取注册帐户的价目表：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

对于 Microsoft 客户协议，请使用以下部分中的信息。 它提供用于 Microsoft 客户协议的字段属性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>计费帐户中计费配置文件范围的价目表

已更新的按计费帐户获取价目表 API 获取 CSV 格式的价目表。 若要在 MCA 的计费配置文件范围获取价目表：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 的注册范围内，API 响应和属性是相同的。 这些属性对应于相同的 MCA 属性。

下表提供了 [Azure 资源管理器价目表 API](/rest/api/consumption/pricesheet) 的旧属性以及相同的新属性。

| 旧的 Azure 资源管理器价目表 API 属性  | 新的 Microsoft 客户协议价目表 API 属性   | 说明 |
| --- | --- | --- |
| 测定仪 ID | _meterId_ | 计量的唯一标识符。 与 meterID 相同。 |
| 计量器名称 | meterName | 计量的名称。 计量器代表 Azure 服务的可部署资源。 |
| 计量器类别  | 服务 | 计量的分类类别的名称。 与 Microsoft 客户协议价目表中的服务相同。 确切的字符串值不同。 |
| 计量器子类别 | meterSubCategory | 计量子分类类别的名称。 基于服务中的高级功能集差异分类。 例如“基本 SQL 数据库”与“标准 SQL 数据库”。 |
| 计量器区域 | meterRegion | &nbsp;  |
| 单位 | _不适用_ | 可从 unitOfMeasure 分析。 |
| 度量单位 | unitOfMeasure | &nbsp;  |
| 部件号 | _不适用_ | 如果不使用部件号，可以改用 productOrderName 和 MeterID 来唯一标识计费配置文件的价格。 字段在 MCA 发票中列出，而不是 MCA 发票中的部件号。 |
| 单价 | unitPrice | Microsoft 客户协议单价。 |
| 货币代码 | pricingCurrency | Microsoft 客户协议使用定价货币和计费货币来表示价格。 货币代码与 Microsoft 客户协议中的 pricingCurrency 相同。 |
| 附送数量 | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 将显示零值。 |
|  产品/服务 ID  | productOrderName | 如果不使用 OfferID，可以改用 productOrderName。 但是，与 OfferID 不同，productOrderName 和 meter 确定 Microsoft 客户协议中的定价。 与旧式注册中的 meterId 和 OfferID 相关。 |

Microsoft 客户协议的价格定义方式不同于企业协议。 企业注册中服务的价格对于产品、部件号、计量器和套餐是唯一的。 Microsoft 客户协议中不使用部件号。

Microsoft 客户协议中的 Azure 消耗服务价格对于 productOrderName 和 meterID 是唯一的。 它们表示服务计量器和产品计划。

若要在使用情况详细信息 API 中核对价目表和使用情况，可以使用 productOrderName 和 meterID。

拥有计费配置文件所有者、参与者、读取者和发票管理者权限的用户可以下载价目表。

价目表包括其价格基于用量的服务的价格。 这些服务包括 Azure 消耗服务和市场消耗服务。 每个服务期限结束时的最新价格将会锁定，并应用到单个服务期限内的用量。 对于 Azure 消耗服务，服务期限通常为一个日历月。

### <a name="retired-price-sheet-api-fields"></a>已停用的价目表 API 字段

以下字段在 Microsoft 客户协议价目表 API 中不可用，或者具有相同的字段。

|已停用的字段| 说明|
|---|---|
| billingPeriodId | 不适用。 对应于 MCA 的 InvoiceId。 |
| offerID | 不适用。 对应于 MCA 中的 productOrderName。 |
| meterCategory  | 不适用。 对应于 MCA 中的 Service。 |
| 单位 | 不适用。 可从 unitOfMeasure 分析。 |
| currencyCode | 与 MCA 中的 pricingCurrency 相同。 |
| meterLocation | 与 MCA 中的 meterRegion 相同。 |
| partNumber partnumber | 不适用，因为 MCA 发票中不会列出部件号。 如果不使用部件号，可以改用 meterId 和 productOrderName 的组合来唯一标识价格。 |
| totalIncludedQuantity | 不适用。 |
| pretaxStandardRate  | 不适用。 |

## <a name="reservation-instance-charge-api-replaced"></a>已替换的预留实例费用 API

可以通过[预留实例费用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) 获取预留项购买内容的计费交易。 新 API 包含所有购买内容，其中包括第三方市场产品/服务。 所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。 预留实例费用 API 已替换为交易 API。

若要通过交易 API 获取预留项购买交易：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>已替换的建议 API

预留实例购买建议 API 提供过去 7 天、30 天或 60 天的虚拟机使用情况。 API 还提供预留项购买建议。 其中包括：

- [共享的预留实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [单个预留实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。 上面列出的预留建议 API 已替换为 [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API。

若要通过预留建议 API 获取预留建议：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>已替换的预留使用情况 API

可以通过预留实例使用情况 API 获取注册中的预留使用情况。 如果注册中有多个预留实例，则还可以使用此 API 获取所有预留实例购买内容的使用情况。

其中包括：

- [预留实例使用情况详细信息](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [预留实例使用情况摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有消耗 API 已替换为使用 Azure AD 进行身份验证和授权的本机 Azure API。 有关调用 Azure REST API 的详细信息，请参阅 [REST 入门](/rest/api/azure/#create-the-request)。 上面列出的预留建议 API 已替换为 [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails) 和 [Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) API。

若要通过预留详细信息 API 获取预留详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要通过预留摘要 API 获取预留摘要：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>从 Cloudyn 转移到成本管理

使用 [Cloudyn](https://cloudyn.com) 的组织应开始使用 [Azure 成本管理](https://azure.microsoft.com/services/cost-management/)来解决任何成本管理需求。 无需任何载入过程和经过 8 小时的延迟，即可在 Azure 门户中使用成本管理。 有关详细信息，请参阅[成本管理文档](../index.yml)。

使用 Azure 成本管理可以：

- 查看一段时间内的成本并将其与预定义的预算进行比较。 分析每日成本模式，以识别并阻止支出异常。 按标记、资源组、服务和位置细分成本。
- 创建预算以针对用量和成本设置限制，并在即将达到重要阈值时收到通知。 使用操作组设置自动化，以触发自定义事件，并基于条款强制实施硬性限制。
- 根据 Azure 顾问的建议优化成本和用量。 通过预留、缩小利用不足的虚拟机，以及删除未使用的资源以保持在预算范围内，找出采购优化措施。
- 计划成本和用量数据导出，每日将一个 CSV 文件发布到存储帐户。 自动与外部系统集成，使计费数据保持同步和最新。

## <a name="power-bi-integration"></a>Power BI 集成

还可以使用 Power BI 来报告成本。 可以使用 Power BI Desktop 的 [Azure 成本管理连接器](/power-bi/desktop-connect-azure-cost-management)创建强大的自定义报表，以便更好地了解 Azure 支出。 Azure 成本管理连接器目前支持签署了 Microsoft 客户协议或企业协议 (EA) 的客户。

## <a name="next-steps"></a>后续步骤

- 阅读[成本管理文档](../index.yml)，了解如何监视和控制 Azure 支出。 或者，了解如何使用成本管理优化资源的使用。
