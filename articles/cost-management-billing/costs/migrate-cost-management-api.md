---
title: 将 EA 迁移到 Microsoft 客户协议 Api-Azure
description: 本文可帮助你了解将 Microsoft 企业协议（EA）迁移到 Microsoft 客户协议的后果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/25/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 0dfb7245135097780f027185e4037b6cafbae690
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/15/2020
ms.locfileid: "75989353"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>从企业协议迁移到 Microsoft 客户协议 Api

本文可帮助你了解企业协议（EA）与 Microsoft 客户协议（MCA）帐户之间的数据结构、API 和其他系统集成差异。 Azure 成本管理支持两种帐户类型的 Api。 继续之前，请查看 Microsoft 客户协议的[设置计费帐户](../manage/mca-setup-account.md)。

具有现有 EA 帐户的组织应与设置 MCA 帐户一起查看本文。 以前，续订 EA 帐户需要执行一些最少的工作才能从旧注册迁移到新注册。 但是，迁移到 MCA 帐户需要额外的工作。 额外的工作是因为基础计费子系统发生了变化，这会影响所有与成本相关的 Api 和服务产品。

## <a name="mca-apis-and-integration"></a>MCA Api 和集成

MCA Api 和新的集成使你能够：

- 通过本机 Azure Api 提供完整的 API 可用性。
- 在单个帐单帐户中配置多张发票。
- 使用 Azure 服务使用情况、第三方 Marketplace 使用和 Marketplace 购买来访问组合 API。
- 使用 Azure 成本管理查看计费配置文件（与注册相同）的成本。
- 访问新的 Api 以显示成本，在成本超出预定义阈值时获得通知，并自动导出原始数据。

## <a name="migration-checklist"></a>迁移核对清单

以下各项可帮助你过渡到 MCA Api。

- 熟悉新的[Microsoft 客户协议计费帐户](../understand/mca-overview.md)。
- 确定要使用的 Api，并查看以下部分中所替换的 Api。
- 熟悉[Azure 资源管理器 REST api](/rest/api/azure)。
- 如果尚未使用 Azure 资源管理器 Api，请在[Azure AD 中注册客户端应用](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 更新任何编程代码以[使用 Azure AD 身份验证](/rest/api/azure/#create-the-request)。
- 更新任何编程代码，将 EA API 调用替换为 MCA API calls。
- 更新错误处理以使用新的错误代码。
- 查看其他集成产品，如 Cloudyn 和 Power BI，以了解其他所需的操作。

## <a name="ea-apis-replaced-with-mca-apis"></a>将 EA Api 替换为 MCA Api

EA Api 使用 API 密钥进行身份验证和授权。 MCA Api 使用 Azure AD 身份验证。

| 用途 | EA API | MCA API |
| --- | --- | --- |
| 余额和信用额度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 用法（JSON） | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用情况（CSV） | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 使用（CSV） | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 计费周期 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 价目表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | BillingAccounts/billingProfiles/价目表/default/下载 format = json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download 格式 = json|csv billingAccounts/。/billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 预留项购买 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 预留建议 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 预订使用情况 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> Azure 服务和第三方 Marketplace 使用适用于使用[情况详细信息 API](/rest/api/consumption/usagedetails)。

以下 Api 适用于 MCA 计费帐户：

| 用途 | Microsoft 客户协议（MCA） API |
| --- | --- |
| 计费帐户<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 计费配置文件<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 发票部分<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 发票 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 计费订阅 | {scope}/billingSubscriptions |

<sup>2</sup> api 返回对象列表，这些对象是范围，其中 Azure 门户和 api 中的成本管理经验。 有关成本管理范围的详细信息，请参阅[了解和使用作用域](understand-work-scopes.md)。

如果你使用任何现有的 EA Api，则需要对其进行更新以支持 MCA 计费帐户。 下表显示了其他集成更改：

| 用途 | 旧产品 | 新产品 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 消费税 Insights](/power-bi/desktop-connect-azure-consumption-insights)内容包和连接器 | [Microsoft Azure 使用见解 Power BI 应用](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)和[Azure 使用见解连接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>用于获取平衡和信用额度的 Api

[获取余额汇总](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)API 提供每月摘要：

- 余额
- 新购买
- Azure Marketplace 服务费用
- 调整
- 服务超额费用

所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。

获取余额汇总 API 替换为 billingAccounts/billingProfiles/availableBalance API。

使用可用的平衡 API 获取可用余额：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>用于获取成本和使用量的 Api

使用以下 Api 从 Azure 服务使用情况、第三方 Marketplace 使用情况和其他 Marketplace 购买成本中获取成本的每日细分。 为 Azure 服务和第三方 Marketplace 使用合并了以下单独的 Api。 旧的 Api 将替换为[usageDetails/](/rest/api/consumption/usagedetails) API。 它添加了 "Marketplace 购买"，后者之前仅在 "余额汇总" 中显示。

- [获取使用情况详细信息/下载](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/提交](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取使用情况详细信息/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取 marketplace 商店费用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [获取 marketplace 商店费用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。

前面的所有 Api 均替换为使用情况/使用情况详细信息 API。

使用使用情况详细信息 API 获取使用情况详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

使用情况详细信息 API 与所有成本管理 Api 一样，可用于多个作用域。 对于开票成本，如以往在注册级别接收的，请使用计费配置文件范围。  有关成本管理范围的详细信息，请参阅[了解和使用作用域](understand-work-scopes.md)。

| 类型 | ID 格式 |
| --- | --- |
| 计费帐户 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 计费配置文件 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 订阅 | `/subscriptions/{subscriptionId}` |
| 资源组 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

使用以下 querystring 参数更新任何编程代码。

| 旧参数 | 新参数 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支持 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

还会更改响应的正文。

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

包含已从数据更改为_值_的使用记录数组的属性名称。 每条记录都有一个简单的详细属性列表。 但是，现在每个记录现在都位于名为_properties_的嵌套属性中，但标记除外。 新结构与其他 Azure Api 一致。 某些属性名称已更改。 下表显示了相应的属性。

| 旧属性 | 新属性 | 说明 |
| --- | --- | --- |
| AccountId | N/A | 不跟踪订阅创建者。 使用 invoiceSectionId （与 departmentId 相同）。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | N/A | 不跟踪订阅创建者。 使用 invoiceSectionName （与 departmentName 相同）。 |
| AdditionalInfo | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 请注意，这些属性为相对。 如果 isAzureCreditEnabled 为 true，则 ChargesBilledSeparately 将为 false。 |
| ConsumedQuantity | quantity | &nbsp; |
| ConsumedService | consumedService | 确切的字符串值可能不同。 |
| ConsumedServiceId | 无 | &nbsp; |
| CostCenter | costCenter | &nbsp; |
| 日期和 usageStartDate | date | &nbsp;  |
| 天 | 无 | 分析日开始日期。 |
| DepartmentId | invoiceSectionId | 精确值不同。 |
| DepartmentName | invoiceSectionName | 确切的字符串值可能不同。 如果需要，请配置发票部分以匹配部门。 |
| ExtendedCost 和成本 | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 为周期性费用 | 无 | &nbsp;  |
| 位置 | location | &nbsp;  |
| MeterCategory | meterCategory | 确切的字符串值可能不同。 |
| 计量 ID | meterId | 确切的字符串值不同。 |
| MeterName | meterName | 确切的字符串值可能不同。 |
| MeterRegion | meterRegion | 确切的字符串值可能不同。 |
| MeterSubCategory | meterSubCategory | 确切的字符串值可能不同。 |
| 月 | 无 | 分析月份开始日期。 |
| 产品/服务名称 | 无 | 使用 publisherName 和 productOrderName。 |
| OfferID | 无 | &nbsp;  |
| 订单编号 | 无 | &nbsp;  |
| PartNumber | 无 | 使用 meterId 和 productOrderName 可唯一标识价格。 |
| 计划名称 | productOrderName | &nbsp;  |
| 产品 | 产品 |   |
| ProductId | productId | 确切的字符串值不同。 |
| 发布者名称 | publisherName | &nbsp;  |
| ResourceGroup | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 确切的字符串值不同。 |
| ResourceLocation | resourceLocation | &nbsp;  |
| ResourceLocationId | 无 | &nbsp;  |
| ResourceRate | effectivePrice | &nbsp;  |
| ServiceAdministratorId | N/A | &nbsp;  |
| ServiceInfo1 | serviceInfo1 | &nbsp;  |
| ServiceInfo2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 确切的字符串值可能不同。 |
| ServiceTier | meterSubCategory | 确切的字符串值可能不同。 |
| StoreServiceIdentifier | N/A | &nbsp;  |
| SubscriptionGuid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| SubscriptionName | subscriptionName | &nbsp;  |
| 标记 | 标记 | Tags 属性应用于根对象，而不是嵌套属性属性。 |
| UnitOfMeasure | unitOfMeasure | 确切的字符串值不同。 |
| usageEndDate | date | &nbsp;  |
| 年 | 无 | 从日期分析年份。 |
| (new) | billingCurrency | 用于收费的货币。 |
| (new) | billingProfileId | 计费配置文件的唯一 ID （与注册相同）。 |
| (new) | billingProfileName | 计费配置文件的名称（与注册的名称相同）。 |
| (new) | chargeType | 用于区分 Azure 服务使用情况、Marketplace 使用情况和购买情况。 |
| (new) | invoiceId | 发票的唯一 ID。 对于当前的、开月为空。 |
| (new) | publisherType | 购买的出版商类型。 空以供使用。 |
| (new) | serviceFamily | 购买类型。 空以供使用。 |
| (new) | servicePeriodEndDate | 已购买服务的结束日期。 |
| (new) | servicePeriodStartDate | 购买的服务的开始日期。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>计费周期 API 替换为发票 API

MCA 计费帐户不使用计费期间。 相反，他们使用发票将成本范围限制为特定的计费周期。 [计费周期 api](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)替换为发票 api。 所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。

若要通过发票 API 获取发票：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>价目表 Api

本部分讨论现有的价目表 Api，并提供移动到 Microsoft 客户协议的价目表 API 的建议。 它还讨论了 Microsoft 客户协议的价目表，并说明了价目表中的字段。 [企业获取价格表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)和[企业获取计费周期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)Api 由 Microsoft 客户协议（billingAccounts/billingProfiles/价目表）的价目表 api 替代。 新的 API 支持 JSON 和 CSV 格式（采用异步 REST 格式）。 所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。

### <a name="billing-enterprise-apis"></a>计费企业 Api

你使用了计费企业 Api 与企业注册以获取价格和计费周期信息。 Azure Active Directory web 令牌使用身份验证和授权。

若要获取具有价目表和计费周期 Api 的指定企业注册的适用价格：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>适用于 Microsoft 客户协议的价目表 API

使用 Microsoft 客户协议的价目表 API 查看所有 Azure 消耗和 Marketplace 消耗服务的价格。 为计费配置文件显示的价格适用于属于计费配置文件的所有订阅。

使用价目表 API 以 CSV 格式查看所有 Azure 消耗服务价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

使用价目表 API 以 JSON 格式查看所有 Azure 消耗服务价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用 API 将返回整个帐户的价目表。 不过，您也可以获取 PDF 格式的价目表的精简版本。 该摘要包括针对特定发票计费的 Azure 消耗和 Marketplace 消耗服务。 发票由 {invoiceId} 标识，这与发票摘要 PDF 文件中显示的**发票编号**相同。 下面提供了一个示例。

![显示与 InvoiceId 相对应的发票号的示例图像](./media/migrate-cost-management-api/invoicesummary.png)

用 CSV 格式的价目表 API 查看发票信息：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要以 JSON 格式查看带有价目表 API 的发票信息：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

你还可以查看当前打开的计费周期或服务期间内任何 Azure 消耗或 Marketplace 消耗服务的预计价格。

使用 CSV 格式的价目表 API 查看消耗服务的预计价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

使用 JSON 格式的价目表 API 查看消耗服务的估计价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客户协议价目表的 Api 是*异步 REST api*。 Api 的响应已从较旧的同步 Api 更改。 还更改了 API 响应的正文。

#### <a name="old-response-body"></a>旧响应正文

下面是同步 REST API 响应的一个示例：

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

Api 支持[AZURE REST 异步](../../azure-resource-manager/management/async-operations.md)格式。 使用 GET 调用 API，会收到以下响应：

```
No Response Body

HTTP Status 202 Accepted
```

以下标头随输出位置一起发送：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

对位置进行另一个 GET 调用。 在操作达到完成或失败状态之前，对 GET 调用的响应是相同的。 完成后，对获取调用位置的响应会返回下载 URL。 就像在同一时间执行操作一样。 下面是一个示例：

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

客户端还可以发出 `Azure-AsyncOperation`的 GET 调用。 终结点返回操作的状态。

下表显示了旧企业获取价目表 API 中的字段。 它包括 Microsoft 客户协议新价目表中的相应字段：

| 旧属性 | 新属性 | 说明 |
| --- | --- | --- |
| billingPeriodId  | _不适用_ | 不适用。 对于 Microsoft 客户协议，发票和相关价目表取代了 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 确切的字符串值可能不同。 |
| includedQuantity  | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 |
| partNumber  | _不适用_ | 相反，请使用 productOrderName （与 offerID）和 meterID 的组合。 |
| unitPrice  | unitPrice | 单价适用于在 Microsoft 客户协议中使用的服务。 |
| currencyCode  | pricingCurrency | Microsoft 客户协议的价格表示形式为定价货币和计费货币。 CurrencyCode 对应于 Microsoft 客户协议中的 pricingCurrency。 |
| offerID | productOrderName | 可以不使用 OfferID，而是使用 productOrderName，但不能与 OfferID 相同。 但是，productOrderName 和计量确定了与传统注册中的 meterId 和 OfferID 相关的 Microsoft 客户协议中的定价。 |

## <a name="consumption-price-sheet-api-operations"></a>消耗价目表 API 操作

对于企业协议，你使用了消耗价目表 API [get](/rest/api/consumption/pricesheet/get) ，并按订阅者或计费周期为范围[获取计费期间](/rest/api/consumption/pricesheet/getbybillingperiod)操作。 API 使用 Azure 资源管理身份验证。

使用价目表 API 获取范围的价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

使用价目表 API 按计费周期获取价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

请使用以下 Microsoft 客户协议，而不是上述 API 终结点：

**适用于 Microsoft 客户协议的价目表 API （异步 REST API）**

此 API 适用于 Microsoft 客户协议，它提供了其他属性。

**计费帐户中计费配置文件范围的价目表**

此 API 是现有的 API。 已对其进行了更新，以便为计费帐户中的计费配置文件提供价目表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>按计费帐户的作用域的价目表

当你获取计费帐户中注册范围内的价目表时，将使用 Azure 资源管理器身份验证。

若要在帐单帐户中获取注册帐户的价目表，请执行以下操作：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

对于 Microsoft 客户协议，请使用以下部分中的信息。 它提供用于 Microsoft 客户协议的字段属性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>计费帐户中计费配置文件范围的价目表

按计费帐户 API 更新的价目表以 CSV 格式获取价目表。 若要获取 MCA 计费配置文件范围的价目表：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 的注册范围内，API 响应和属性完全相同。 属性与相同的 MCA 属性相对应。

下表中提供了[Azure 资源管理器价目表 api](/rest/api/consumption/pricesheet)的旧属性和相同的新属性。

| 旧 Azure 资源管理器价目表 API 属性  | 新的 Microsoft 客户协议价目表的 API 属性   | Description |
| --- | --- | --- |
| 测定仪 ID | _meterId_ | 计量的唯一标识符。 与 meterID 相同。 |
| 计量名称 | meterName | 计量的名称。 计量表示可部署的 Azure 服务资源。 |
| 计量类别  | 服务 | 计量的分类类别的名称。 与 Microsoft 客户协议价目表中的服务相同。 确切的字符串值不同。 |
| 计量子类别 | meterSubCategory | 计量子分类类别的名称。 基于服务中高级功能集差异分类。 例如，基本 SQL DB 与标准 SQL DB。 |
| 计量区域 | meterRegion | &nbsp;  |
| 单位 | _不适用_ | 可以从 unitOfMeasure 分析。 |
| 计量单位 | unitOfMeasure | &nbsp;  |
| 商品编号 | _不适用_ | 使用 productOrderName 和 MeterID 来唯一确定计费配置文件的价格，而不是使用部分号。 字段在 MCA 发票上列出，而不是在 MCA 发票中列出。 |
| 单价 | unitPrice | Microsoft 客户协议单价。 |
| 货币代码 | pricingCurrency | Microsoft 客户协议以定价货币和计费币种表示价格。 货币代码与 Microsoft 客户协议中的 pricingCurrency 相同。 |
| 包括的量 | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 显示值为零的值。 |
|  产品/服务 ID  | productOrderName | 使用 productOrderName 而不是 OfferID。 不同于 OfferID，但 productOrderName 和计量确定了 Microsoft 客户协议中的定价。 与传统注册中的 meterId 和 OfferID 相关。 |

Microsoft 客户协议的价格定义方式不同于企业协议。 企业注册中服务的价格对于产品、部件编号、计量和提议是唯一的。 Microsoft 客户协议中未使用部件编号。

Microsoft 客户协议中的 Azure 消耗服务价格对于 productOrderName 和 meterID 是唯一的。 它们表示服务指示器和产品计划。

若要在价目表和使用情况详细信息 API 中的使用情况之间进行协调，可以使用 productOrderName 和 meterID。

具有帐单配置文件所有者、参与者、读者和发票管理器权限的用户可以下载价目表。

价目表包括价格基于使用情况的服务价格。 服务包括 Azure 消耗和 Marketplace 消耗。 每个服务期间结束时的最新价格会锁定，并应用于单个服务期间的使用情况。 对于 Azure 消耗服务，服务期间通常为日历月份。

### <a name="retired-price-sheet-api-fields"></a>淘汰价目表 API 字段

以下字段在 Microsoft 客户协议价目表的 Api 中不可用或具有相同的字段。

|停用字段| Description|
|---|---|
| billingPeriodId | 不适用。 对应于 MCA 的 InvoiceId。 |
| offerID | 不适用。 对应于 MCA 中的 productOrderName。 |
| meterCategory  | 不适用。 对应于 MCA 中的服务。 |
| 单位 | 不适用。 可以从 unitOfMeasure 分析。 |
| currencyCode | 与 MCA 中的 pricingCurrency 相同。 |
| meterLocation | 与 MCA 中的 meterRegion 相同。 |
| partNumber partnumber | 不适用，因为 MCA 发票中未列出部件编号。 使用 meterId 和 productOrderName 组合来唯一标识价格，而不是使用部分号。 |
| totalIncludedQuantity | 不适用。 |
| pretaxStandardRate  | 不适用。 |

## <a name="reservation-instance-charge-api-replaced"></a>已替换预订实例费用 API

可以通过预订[实例费用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)获取预订购买的计费交易。 新的 API 包括所有购买，包括第三方 Marketplace 产品/服务。 所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。 保留实例费用 API 被事务 API 替换。

若要获取预留购买事务和事务 API：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>替换建议 Api

保留实例购买建议 Api 提供过去7、30或60天的虚拟机使用情况。 Api 还提供预订购买建议。 其中包括：

- [共享保留实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [单个保留实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。 前面列出的预订建议 Api 被替换为[reservationRecommendations/](/rest/api/consumption/reservationrecommendations/list) api。

若要获取预订建议 API 的预订建议：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>保留使用情况 Api 已替换

你可以使用预留实例使用量 API 获取注册中的保留使用情况。 如果注册中有多个预订实例，还可以使用此 API 来获取所有保留实例购买的使用情况。

其中包括：

- [预留实例使用量详细信息](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for-reserved-instance-usage-details)
- [预留实例使用量摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有消耗 Api 都替换为使用 Azure AD 进行身份验证和授权的本机 Azure Api。 有关调用 Azure REST Api 的详细信息，请参阅[REST](/rest/api/azure/#create-the-request)入门。 前面列出的预订建议 Api 被替换为[reservationDetails/](/rest/api/consumption/reservationsdetails)和[reservationSummaries](/rest/api/consumption/reservationssummaries) api。

若要获取预订详细信息 API 的保留详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要获取预订摘要 API 的保留摘要：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>从 Cloudyn 转到成本管理

使用[Cloudyn](https://cloudyn.com)的组织应开始使用[Azure 成本管理](https://azure.microsoft.com/services/cost-management/)，以满足任何成本管理需求。 成本管理在 Azure 门户中提供，无需任何载入和8小时的延迟。 有关详细信息，请参阅[成本管理文档](../index.yml)。

使用 Azure 成本管理可以：

- 根据预定义的预算，查看一段时间内的成本。 分析日常成本模式，识别和停止异常支出。 按标记、资源组、服务和位置降低成本。
- 创建预算以设置对使用情况和成本的限制，并在达到重要阈值时获得通知。 使用操作组设置自动化，以触发自定义事件并根据情况实施硬限制。
- 使用 Azure 顾问提供的建议优化成本和使用情况。 发现使用保留、缩小的虚拟机的购买优化，并删除未使用的资源以保留在预算内。
- 计划每日将一个 CSV 文件发布到存储帐户的成本和使用情况数据导出。 自动与外部系统集成，使计费数据保持同步和最新。

## <a name="power-bi-integration"></a>Power BI 集成

你还可以使用 Power BI 进行成本报告。 适用于 Power BI Desktop 的[Azure 成本管理连接器](/power-bi/desktop-connect-azure-cost-management)可用于创建功能强大的自定义报表，有助于更好地了解 Azure 支出。 Azure 成本管理连接器目前支持使用 Microsoft 客户协议或企业协议（EA）的客户。

## <a name="next-steps"></a>后续步骤

- 阅读[成本管理文档](../index.yml)，了解如何监视和控制 Azure 支出。 或者，如果你想要使用成本管理优化资源使用。
