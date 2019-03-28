---
title: 企业协议从迁移到 Microsoft 客户协议 Api-Azure |Microsoft Docs
description: 本文可帮助你了解迁移到 Microsoft 客户协议的 Microsoft 企业协议 (EA) 的后果。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: d9c5d731120f939cf7fb28c718cc4159a3702e44
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518773"
---
# <a name="migrate-from-enterprise-agreement-to-microsoft-customer-agreement-apis"></a>企业协议从迁移到 Microsoft 客户协议 Api

本文可帮助您了解数据结构、 API 和其他系统集成区别企业协议 (EA) 和 Microsoft 客户协议 (MCA) 帐户。 Azure 成本管理支持这两种帐户类型的 Api。 审阅[设置为计费帐户](../billing/billing-mca-setup-account.md)Microsoft 客户协议文章，然后再继续。

具有现有 EA 帐户的组织应查看与设置 MCA 帐户结合使用这篇文章。 以前，续订 EA 帐户需要很少的操作将从旧注册移到一个新。 但是，迁移到 MCA 帐户需要进行其他工作。 由于基础计费子系统，这会影响所有与成本相关的 Api 和服务产品/服务的更改是额外的努力。

## <a name="mca-apis-and-integration"></a>MCA Api 和集成

MCA Api 和新的集成使您能够：

- 已通过 Azure 的本机 Api 的完整 API 可用性。
- 在一个计费帐户中配置多张发票。
- 访问 Azure 服务使用情况、 第三方 Marketplace 的使用情况，与 Marketplace 购买内容的组合的 API。
- 查看整个计费配置文件 （与注册相同） 的成本使用 Azure 成本管理。
- 访问新的 Api 来展示成本，成本超过预定义的阈值，并且自动导出原始数据时获得通知。

## <a name="migration-checklist"></a>迁移清单

以下项帮助转换到 MCA Api。

- 了解与新[计费帐户的 Microsoft 客户协议](../billing/billing-mca-overview.md)。
- 确定哪些 Api 使用，并查看哪些替换为以下部分中。
- 自己应熟悉[Azure 资源管理器 REST Api](/rest/api/azure)。
- 如果尚未使用 Azure 资源管理器 Api[与 Azure AD 注册客户端应用](/rest/api/azure/#register-your-client-application-with-azure-ad)。
- 更新到任何编程代码[使用 Azure AD 身份验证](/rest/api/azure/#create-the-request)。
- 更新任何编程代码以替换 MCA API 调用为 EA API 调用。
- 更新错误处理，以使用新的错误代码。
- 查看其他集成的产品/服务，Cloudyn 和 Power BI 等其他所需操作。

## <a name="ea-apis-replaced-with-mca-apis"></a>EA Api 替换为 MCA Api

EA Api 使用 API 密钥身份验证和授权。 MCA Api 使用 Azure AD 身份验证。

| 用途 | EA API | MCA API |
| --- | --- | --- |
| 余额和信用额度 | [/balancesummary](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) | Microsoft.Billing/billingAccounts/billingProfiles/availableBalanceussae |
| 使用情况 (JSON) | [/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)[/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format) | [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 使用情况 (CSV) | [/usagedetails/download](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)[/usagedetails/submit](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| Marketplace 使用情况 (CSV) | [/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)[/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) | [Microsoft.Consumption/usageDetails/download](/rest/api/consumption/usagedetails)<sup>1</sup> |
| 计费周期 | [/billingperiods](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 价目表 | [/pricesheet](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) | Microsoft.Billing/billingAccounts/billingProfiles/pricesheet/default/download format=json|csv Microsoft.Billing/billingAccounts/…/billingProfiles/…/invoices/… /pricesheet/default/download 格式 = json|csv Microsoft.Billing/billingAccounts/../billingProfiles/../providers/Microsoft.Consumption/pricesheets/download  |
| 预订购买 | [/reservationcharges](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges) | Microsoft.Billing/billingAccounts/billingProfiles/transactions |
| 预留建议 | [/SharedReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)[/](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)[SingleReservationRecommendations](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations) | [Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) |
| 预订使用情况 | [/reservationdetails](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)[/reservationsummaries](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage) | [Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) |

<sup>1</sup> azure 服务和第三方 Marketplace 使用情况[使用情况详细信息 API](/rest/api/consumption/usagedetails)。

以下 Api 可供 MCA 计费帐户：

| 用途 | Microsoft 客户协议 (MCA) API |
| --- | --- |
| 计费帐户<sup>2</sup> | Microsoft.Billing/billingAccounts |
| 计费配置文件<sup>2</sup> | Microsoft.Billing/billingAccounts/billingProfiles |
| 发票部分<sup>2</sup> | Microsoft.Billing/billingAccounts/invoiceSections |
| 发票 | Microsoft.Billing/billingAccounts/billingProfiles/invoices |
| 计费的订阅 | {scope}/billingSubscriptions |

<sup>2</sup> Api 返回的对象，后者是作用域，其中成本管理体验在 Azure 门户和 Api 操作的列表。 有关成本管理作用域的详细信息，请参阅[了解和使用作用域](understand-work-scopes.md)。

如果使用任何现有 EA Api，你需要它们更新为支持 MCA 计费帐户。 下表显示了其他集成的更改：

| 用途 | 旧的产品/服务 | 新产品/服务 |
| --- | --- | --- |
| Cloudyn | [Cloudyn.com](https://www.cloudyn.com) | [Azure 成本管理](https://azure.microsoft.com/services/cost-management/) |
| Power BI | [Microsoft 使用情况见解](/power-bi/desktop-connect-azure-consumption-insights)内容包和连接器 | [Microsoft Azure 使用情况见解 Power BI 应用](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)和[Azure Consumption Insights 连接器](/power-bi/desktop-connect-azure-consumption-insights) |

## <a name="apis-to-get-balance-and-credits"></a>若要获取余额和信用额度的 Api

[获取余额摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)API 使你的每月摘要：

- 余额
- 新购买
- Azure Marketplace 服务费用
- 调整
- 服务超额费用

所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。

获取余额摘要 API 替换为 Microsoft.Billing/billingAccounts/billingProfiles/availableBalance API。

若要获取可用余额与可用余额 API:

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/availableBalances?api-version=2018-11-01-preview` |

## <a name="apis-to-get-cost-and-usage"></a>若要获取成本和使用情况 Api

从 Azure 服务使用情况、 第三方 Marketplace 用法和其他 Marketplace 购买内容与以下 Api 获取成本的日常明细。 以下的单独 Api 已合并的 Azure 服务和第三方 Marketplace 使用情况。 替换为旧的 Api [Microsoft.Consumption/usageDetails](/rest/api/consumption/usagedetails) API。 它会添加 Marketplace 购买内容，这以前仅摘要中显示余额为日期。

- [获取使用情况详细信息/下载](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/提交](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#csv-format)
- [获取使用情况详细信息/usagedetails](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取使用情况详细信息/usagedetailsbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail#json-format)
- [获取 marketplace 应用商店费用/marketplacecharges](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)
- [获取 marketplace 应用商店费用/marketplacechargesbycustomdate](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)

所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。

所有上述 Api 替换为消耗/使用情况详细信息 API。

若要获取使用情况与使用情况详细信息 API 的详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?api-version=2019-01-01` |

使用情况详细信息 API，所有成本管理 Api，都是可在多个作用域。 对于已开票成本，因为传统上会收到在注册级别，使用计费的配置文件范围。  有关成本管理作用域的详细信息，请参阅[了解和使用作用域](understand-work-scopes.md)。

| 类型 | ID 格式 |
| --- | --- |
| 计费帐户 | `/Microsoft.Billing/billingAccounts/{billingAccountId}` |
| 计费配置文件 | `/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}` |
| 订阅 | `/subscriptions/{subscriptionId}` |
| 资源组 | `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}` |

使用以下查询字符串参数更新任何编程代码。

| 旧的参数 | 新的参数 |
| --- | --- |
| `billingPeriod={billingPeriod}` | 不支持 |
| `endTime=yyyy-MM-dd` | `endDate=yyyy-MM-dd` |
| `startTime=yyyy-MM-dd` | `startDate=yyyy-MM-dd` |

响应正文也发生更改。

旧的响应正文：

```
{
  "id": "string",
  "data": [{...}, ...],
  "nextLink": "string"
}
```

新的响应正文：

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

包含该数组的使用情况记录的属性名称从到的数据更改_值_。 每个记录，用于将简单列表的详细属性。 但是，每个记录现在名为的嵌套属性现提供所有详细信息_属性_，标记。 新的结构是与其他 Azure Api 保持一致。 某些属性名称已更改。 下表显示了相应的属性。

| 旧属性 | 新的属性 | 说明 |
| --- | --- | --- |
| 帐户 ID | 不适用 | 不会跟踪订阅创建者。 使用 invoiceSectionId （与 departmentId 相同）。 |
| AccountNameAccountOwnerId 和 AccountOwnerEmail | 不适用 | 不会跟踪订阅创建者。 使用 invoiceSectionName （与 departmentName 相同）。 |
| 其他信息 | additionalInfo | &nbsp;  |
| ChargesBilledSeparately | isAzureCreditEligible | 请注意，这些属性刚好相反。 如果 isAzureCreditEnabled 为 true，ChargesBilledSeparately 将为 false。 |
| 已使用数量 | quantity | &nbsp; |
| 已使用的服务 | consumedService | 确切的字符串值可能不同。 |
| 已使用的服务 ID | 无 | &nbsp; |
| 成本中心 | costCenter | &nbsp; |
| 日期和 usageStartDate | date | &nbsp;  |
| 天 | 无 | 分析日期的一天。 |
| 部门 ID | invoiceSectionId | 确切的值不同。 |
| 部门名称 | invoiceSectionName | 确切的字符串值可能不同。 如果需要请配置发票部分以匹配部门。 |
| ExtendedCost 和成本 | costInBillingCurrency | &nbsp;  |
| InstanceId | resourceId | &nbsp;  |
| 为周期性费用 | 无 | &nbsp;  |
| 位置 | 位置 | &nbsp;  |
| 计量类别 | meterCategory | 确切的字符串值可能不同。 |
| 计量 ID | meterId | 确切的字符串值而有所不同。 |
| 计量名称 | meterName | 确切的字符串值可能不同。 |
| 计量区域 | meterRegion | 确切的字符串值可能不同。 |
| 计量子类别 | meterSubCategory | 确切的字符串值可能不同。 |
| 月 | 无 | 分析一个月中日期。 |
| 产品/服务名称 | 无 | 使用 publisherName 和 productOrderName。 |
| OfferId | 无 | &nbsp;  |
| 订单编号 | 无 | &nbsp;  |
| PartNumber | 无 | 使用 meterId 和 productOrderName 来唯一标识的价格。 |
| 计划名称 | productOrderName | &nbsp;  |
| 产品 | 产品 |   |
| 产品 ID | productId | 确切的字符串值而有所不同。 |
| 发布者名称 | publisherName | &nbsp;  |
| 资源组 | resourceGroupName | &nbsp;  |
| ResourceGuid | meterId | 确切的字符串值而有所不同。 |
| 资源位置 | resourceLocation | &nbsp;  |
| 资源位置 ID | 无 | &nbsp;  |
| 资源费率 | effectivePrice | &nbsp;  |
| 服务管理员 ID | 不适用 | &nbsp;  |
| 服务信息 1 | serviceInfo1 | &nbsp;  |
| 服务信息 2 | serviceInfo2 | &nbsp;  |
| ServiceName | meterCategory | 确切的字符串值可能不同。 |
| ServiceTier | meterSubCategory | 确切的字符串值可能不同。 |
| 存储服务标识符 | 不适用 | &nbsp;  |
| 订阅 Guid | subscriptionId | &nbsp;  |
| SubscriptionId | subscriptionId | &nbsp;  |
| 订阅名称 | subscriptionName | &nbsp;  |
| 标记 | 标记 | 标记属性适用于根对象，而不是嵌套的属性属性。 |
| 度量单位 | unitOfMeasure | 确切的字符串值而有所不同。 |
| usageEndDate | date | &nbsp;  |
| 年 | 无 | 分析日期的年份。 |
| (新建) | billingCurrency | 使用付费的货币。 |
| (新建) | billingProfileId | 计费 （相同的注册） 的配置文件的唯一 ID。 |
| (新建) | billingProfileName | 计费 （相同的注册） 的配置文件的名称。 |
| (新建) | chargeType | 使用区分 Azure 服务使用情况、 Marketplace 使用情况和购买。 |
| (新建) | invoiceId | 发票的唯一 ID。 当前，打开月为空。 |
| (新建) | publisherType | 购买的发布服务器的类型。 使用情况的为空。 |
| (新建) | serviceFamily | 购买的类型。 使用情况的为空。 |
| (新建) | servicePeriodEndDate | 购买的服务的结束日期。 |
| (新建) | servicePeriodStartDate | 开始日期为购买的服务。 |

## <a name="billing-periods-api-replaced-by-invoices-api"></a>计费周期 API 替换为发票 API

MCA 计费帐户都不使用的计费周期。 相反，它们使用作用域成本到特定计费周期的发票。 [计费周期 API](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)替换为发票 API。 所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。

若要获取发票的发票 api:

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/invoices?api-version=2018-11-01-preview` |

## <a name="price-sheet-apis"></a>价格表 Api

本部分讨论现有价格表 Api，并提供建议，以将移动到价目表 API 为 Microsoft 客户协议。 它还讨论价目表 API 为 Microsoft 客户协议的并介绍了价格表中的字段。 [Enterprise 获得价目表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)并[Enterprise 获得计费期间](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)Api 为 Microsoft 客户协议 (Microsoft.Billing/billingAccounts/billingProfiles 价目表 API 替换为/ 价目表)。 新的 API 支持 JSON 和 CSV 格式，在异步 REST 格式。 所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。

### <a name="billing-enterprise-apis"></a>计费企业 Api

使用企业注册使用计费企业 Api 获取价格和计费周期的信息。 使用 Azure Active Directory web 令牌身份验证和授权。

若要获取的价目表，计费期 Api 指定的企业许可登记表适用价格：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/pricesheet` |
| GET | `https://consumption.azure.com/v2/enrollments/{enrollmentNumber}/billingPeriods/{billingPeriod}/pricesheet` |

### <a name="price-sheet-api-for-microsoft-customer-agreements"></a>价目表 API 为 Microsoft 客户协议的

使用 Microsoft 客户协议价目表 API 来查看所有 Azure 使用情况和 Marketplace 消费服务的价格。 有关计费的配置文件所示的价格适用于属于计费的配置文件的所有订阅。

使用价目表 API 以 CSV 格式查看所有 Azure 消耗 services 价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=csv` |

使用价目表 API 以 JSON 格式查看所有 Azure 消耗 services 价目表数据：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&startDate=2019-01-01&endDate=2019-01-31&format=json` |

使用 API 返回的整个帐户的价格表。 但是，还可以获取 PDF 格式的价目表的精简的版本。 摘要包括针对某一特定发票计费的 Azure 使用情况和 Marketplace 消费服务。 由 {invoiceId} 标识发票与相同**发票编号**发票摘要 PDF 文件中所示。 下面是一个示例。

![到 InvoiceId 显示相对应的发票号的示例图像](./media/migrate-cost-management-api/invoicesummary.png)

若要以 CSV 格式查看使用价目表 API 的发票信息：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要查看使用价目表 API 的发票信息以 JSON 格式：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/2909cffc-b0a2-5de1-bb7b-5d3383764184/billingProfiles/2dcffe0c-ee92-4265-8647-515b8fe7dc78/invoices/{invoiceId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

在当前打开的计费周期或服务期间，还可以查看 Azure 消耗或 Marketplace 消耗的任何服务的估计的价格。

若要以 CSV 格式查看的消费服务使用价目表 API 的估计的价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=csv` |

若要查看估计使用价目表 API 以 JSON 格式的消费服务的价格：

| 方法 | 请求 URI |
| --- | --- |
| POST | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billing AccountId}/billingProfiles/{billingProfileId}/pricesheet/default/download?api-version=2018-11-01-preview&format=json` |

Microsoft 客户协议价格表 api*异步 REST Api*。 从较旧的同步 Api 更改 Api 的响应。 API 响应的正文也发生更改。

#### <a name="old-response-body"></a>旧的响应正文

下面是同步的 REST API 响应的示例：

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

#### <a name="new-response-body"></a>新的响应正文

Api 支持[Azure REST 异步](../azure-resource-manager/resource-manager-async-operations.md)格式。 使用 GET 调用 API 并收到以下响应：

```
No Response Body

HTTP Status 202 Accepted
```

以下标头发送输出的位置：

```
Location:https://management.azure.com/providers/Microsoft.Consumption/operationresults/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Azure-AsyncOperation:https://managment.azure.com/providers/Microsoft.Consumption/operationStatus/{operationId}?sessiontoken=XZDFSnvdkbkdsb==

Retry-After: 10

OData-EntityId: {operationId}

```

请再次获取到的位置调用。 该操作直到完成或失败状态，对 GET 调用的响应都是相同的。 完成后，对 GET 调用位置的响应返回的下载 URL。 就像在同一时间执行该操作。 下面是一个示例：

```
HTTP Status 200

                                    {
                            “id”: “providers/Microsoft.Consumption/operationresults/{operationId}”,
                            “name”: {operationId},
                           “type”: “Microsoft.Consumption/operationResults”,
                           “properties” : {
                                  “downloadUrl”: {urltoblob},
                                  “vaildTill”: “Date”
}
                     }
```

客户端还可以发出 GET 调用`Azure-AsyncOperation`。 终结点返回该操作的状态。

下表显示了较旧的 Enterprise 获得价格表 API 中的字段。 它包括相应的字段中新的价目表的 Microsoft 客户协议：

| 旧属性 | 新的属性 | 说明 |
| --- | --- | --- |
| billingPeriodId  | _不适用_ | 不适用。 Microsoft 客户协议的发票和关联的价目表替换 billingPeriodId 的概念。 |
| meterId  | meterId | &nbsp;  |
| unitOfMeasure  | unitOfMeasure | 确切的字符串值可能不同。 |
| includedQuantity  | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 |
| partNumber  | _不适用_ | 请改用 productOrderName （与 offerId 相同） 和 meterid 的组合。 |
| unitPrice  | unitPrice | 单位价格是适用于使用 Microsoft 客户协议的服务。 |
| currencyCode  | pricingCurrency | Microsoft 客户协议具有中定价货币和开单币种的价格表示形式。 CurrencyCode 与 Microsoft 客户协议中 pricingCurrency 相对应。 |
| offerId | productOrderName | 而不是 OfferId，你可以使用 productOrderName 但与 OfferId 不完全相同。 但是，productOrderName 和计量确定中 Microsoft 客户协议的定价与 meterId 和 Offerid 中旧的注册。 |

## <a name="consumption-price-sheet-api-operations"></a>使用价目表 API 操作

对于企业协议，你使用消耗价目表 API[获取](/rest/api/consumption/pricesheet/get)并[获取的计费周期](/rest/api/consumption/pricesheet/getbybillingperiod)由订阅 Id 或计费周期的作用域的操作。 API 使用 Azure 资源管理身份验证。

若要获取具有价目表 API 的作用域的价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

若要通过计费期间使用价目表 API 获取价目表信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Billing/billingPeriods/{billingPeriodName}/providers/Microsoft.Consumption/pricesheets/default?api-version=2018-10-01` |

而不是上述 API 终结点，请使用 Microsoft 客户协议的以下查询：

**价目表 API 为 Microsoft 客户协议 (异步 REST API)**

此 API 用于 Microsoft 客户协议，它提供了其他属性。

**在计费帐户的计费配置文件作用域的价目表**

此 API 是现有的 API。 它已更新为计费帐户中的计费配置文件提供的价格表。

## <a name="price-sheet-for-a-scope-by-billing-account"></a>通过计费帐户作用域的价目表

在计费帐户中的注册作用域获取价格表时，使用 azure 资源管理器身份验证。

若要在注册帐户的计费帐户中获取价目表：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/65085863/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

Microsoft 客户协议下, 一节中使用的信息。 它提供了用于 Microsoft 客户协议的字段属性。

### <a name="price-sheet-for-a-billing-profile-scope-in-a-billing-account"></a>在计费帐户的计费配置文件作用域的价目表

更新的价目表的计费帐户 API 获取价目表以 CSV 格式。 若要在为 MCA 计费的配置文件范围内获取价目表：

| 方法 | 请求 URI |
| --- | --- |
| GET | `/providers/Microsoft.Billing/billingAccounts/28ae4b7f-41bb-581e-9fa4-8270c857aa5f/billingProfiles/ef37facb-cd6f-437a-9261-65df15b673f9/providers/Microsoft.Consumption/pricesheets/download?api-version=2019-01-01` |

在 EA 的注册范围中，API 响应和属性均相同。 属性对应于相同的 MCA 属性。

较旧的属性[Azure 资源管理器价格表 Api](/rest/api/consumption/pricesheet)和相同的新属性是下表中。

| 旧 Azure 资源管理器价格表 API 属性  | 新的 Microsoft 客户协议价目表 API 属性   | 描述 |
| --- | --- | --- |
| 计量 ID | _meterId_ | 测定仪的唯一标识符。 MeterId 相同。 |
| 计量名称 | meterName | 计量的名称。 指标表示 Azure 服务可部署资源。 |
| 计量类别  | 服务 | 测定仪分类类别的名称。 与 Microsoft 客户协议价目表中的服务相同。 确切的字符串值而有所不同。 |
| 计量子类别 | meterSubCategory | 计量子分类类别的名称。 基于在服务中的高级功能集差异的分类。 例如，基本 SQL DB 的 vs 标准 SQL 数据库。 |
| 计量区域 | meterRegion | &nbsp;  |
| 单位 | _不适用_ | 可以从 unitOfMeasure 分析。 |
| 计量单位 | unitOfMeasure | &nbsp;  |
| 商品编号 | _不适用_ | 而不是 partNumber，使用 productOrderName 和 MeterId 来唯一标识的价格计费的配置文件。 将列出域，而不是在 MCA 发票 partNumber MCA 发票上。 |
| 单位价格 | unitPrice | Microsoft 客户协议单价。 |
| 货币代码 | pricingCurrency | Microsoft 客户协议表示中货币的定价和计费货币的价格。 货币代码是与 Microsoft 客户协议中 pricingCurrency 相同。 |
| 包括的量 | includedQuantity | 不适用于 Microsoft 客户协议中的服务。 显示的值为零。 |
|  产品 ID  | productOrderName | 而不是 OfferId，使用 productOrderName。 与 OfferId，但是 productOrderName 和计量器决定了 Microsoft 客户协议中的定价。 与在旧注册 meterId 和 Offerid。 |

Microsoft 客户协议的价格被定义的方式不同于企业协议。 企业许可登记表中的服务的价格是唯一的产品、 PartNumber、 测定仪和产品/服务。 PartNumber 不在 Microsoft 客户协议中使用。

是 Microsoft 客户协议的一部分的 Azure 消耗服务价格是唯一的 productOrderName 和 meterId。 它们代表服务计量，并在产品计划。

若要对帐价格表和使用情况详细信息 API 中的使用情况之间，可以使用 productOrderName 和 meterId。

已计费所有者、 参与者、 读取器配置文件和发票管理器权限的用户可以下载价目表。

价目表包含其价格基于使用情况的服务的价格。 服务包括 Azure 使用情况和将 Marketplace 消耗。 每个服务周期结束时的最新价格已被锁定，在单个服务期间应用于使用情况。 对于 Azure 消费服务，服务会通常一个日历月。

### <a name="retired-price-sheet-api-fields"></a>已停用的价目表 API 字段

以下字段不是 Microsoft 客户协议价格表 Api 中提供或具有不同的字段。

|已停用的字段| 描述|
|---|---|
| billingPeriodId | 不适用。 对应 MCA InvoiceId。 |
| offerId | 不适用。 对应于 productOrderName MCA 中。 |
| meterCategory  | 不适用。 对应于 MCA 中的服务。 |
| 单位 | 不适用。 可以从 unitOfMeasure 分析。 |
| currencyCode | 与在 MCA pricingCurrency 相同。 |
| meterLocation | 与在 MCA meterRegion 相同。 |
| partNumber partnumber | 不适用，因为 MCA 发票中未列出部件号。 而不是 partnumber，使用的 meterId 和 productOrderName 组合来唯一标识的价格。 |
| totalIncludedQuantity | 不适用。 |
| pretaxStandardRate  | 不适用。 |

## <a name="reservation-instance-charge-api-replaced"></a>保留实例费用 API 替换为

你可以获取计费事务与预订购买[保留实例费用 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-charges)。 新的 API 包括所有购买，包括第三方 Marketplace 产品/服务。 所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。 保留实例费用 API 替换为事务 API。

若要获取预订购买交易与交易记录 API:

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Billing/billingAccounts/{billingAccountId}/billingProfiles/{billingProfileId}/transactions?api-version=2018-11-01-preview` |

## <a name="recommendations-apis-replaced"></a>建议 Api 替换为

保留的实例购买建议 Api 提供上一次 7、 30 或 60 天内的虚拟机使用情况。 Api 还提供了预订购买建议。 这些权限包括：

- [共享预订的实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-shared-reserved-instance-recommendations)
- [单个保留的实例建议 API](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation#request-for-single-reserved-instance-recommendations)

所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。 替换为前面列出的 Api 的保留项建议[Microsoft.Consumption/reservationRecommendations](/rest/api/consumption/reservationrecommendations/list) API。

若要获取预订的预订建议 api 的建议：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationRecommendations?api-version=2019-01-01` |

## <a name="reservation-usage-apis-replaced"></a>替换为预订使用情况 Api

可以在注册中使用保留的实例使用情况 API 来获取预订使用情况。 如果在注册多个保留的实例，还可以获取所有保留的实例购买使用此 API 的使用情况。

这些权限包括：

- [保留的实例使用情况详细信息](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage#request-for--reserved-instance-usage-details)
- [保留的实例使用情况摘要](/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-usage)

所有使用情况 Api 替换为 Azure 的本机 Api，使用 Azure AD 进行身份验证和授权。 调用 Azure REST Api 的详细信息，请参阅[开始使用 REST](/rest/api/azure/#create-the-request)。 替换为前面列出的 Api 的保留项建议[Microsoft.Consumption/reservationDetails](/rest/api/consumption/reservationsdetails)并[Microsoft.Consumption/reservationSummaries](/rest/api/consumption/reservationssummaries) Api。

若要获取预订使用保留的详细信息 API 的详细信息：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationDetails?api-version=2019-01-01` |

若要获取与保留摘要 API 保留摘要：

| 方法 | 请求 URI |
| --- | --- |
| GET | `https://management.azure.com/providers/Microsoft.Consumption/reservationSummaries?api-version=2019-01-01` |



## <a name="move-from-cloudyn-to-cost-management"></a>将从移动 Cloudyn 成本管理

使用的组织[Cloudyn](https://cloudyn.com)应开始使用[Azure 成本管理](https://azure.microsoft.com/services/cost-management/)的任何成本管理需求。 可在 Azure 门户中使用任何载入和八小时延迟成本的管理。 有关详细信息，请参阅[成本管理文档](index.yml)。

使用 Azure 成本管理，你可以：

- 查看一段时间根据预定义的预算成本。 分析每日成本模式识别和防止支出异常。 分解的标记、 资源组、 服务和位置的成本。
- 创建预算，才能使用情况和成本上设置限制，并在接近重要的阈值时获得通知。 设置使用操作组触发自定义事件和强制执行硬性限制，您的方式实现自动化。
- 优化成本和根据 Azure 顾问的建议的使用情况。 发现的预订购买优化、 缩小未充分利用的虚拟机，并删除未使用的资源以保持在预算范围内。
- 计划每日将 CSV 文件发布到你的存储帐户的成本和使用情况数据导出。 自动执行与外部系统，用于保留计费数据保持同步，最新的集成。

## <a name="power-bi-integration"></a>Power BI 集成

如果您为成本报告中使用 Power BI，您需要过渡到以下：

- Microsoft Azure 使用情况见解 Power BI 应用
- Azure Consumption Insights 桌面连接器


连接器被建议的组织来说最大的灵活性。 但是，Power BI 应用也是可用于快速设置的。

- 安装[Microsoft Azure 使用情况见解的 Power BI 应用](https://appsource.microsoft.com/product/power-bi/pbi_azureconsumptioninsights.pbi-azure-consumptioninsights?tab=overview)
- [使用 Azure 使用情况见解连接器连接](/power-bi/desktop-connect-azure-consumption-insights)

较旧的 Consumption Insights 内容包和连接器在注册级别工作。 它需要至少读取访问权限。 新的使用情况见解 Power BI 应用和新的 Azure 使用情况见解连接器是可用于配置文件用户计费。 对成本进行了审阅或跨计费配置文件中查看成本需要其他选项的团队应使用在[成本分析](https://ms.portal.azure.com/#blade/Microsoft_Azure_CostManagement/Menu/costanalysis)Azure 门户。

## <a name="next-steps"></a>后续步骤

- 读取[成本管理文档](index.yml)若要了解如何监视和控制 Azure 支出。 或者，如果你想要优化使用成本管理的资源使用。
