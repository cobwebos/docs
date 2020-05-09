---
title: Azure Enterprise REST API
description: 本文介绍可用于 Azure 企业注册的 REST API。
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: b35937a5f4638e73dd1603dee7ad97e838efac8b
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/01/2020
ms.locfileid: "82692658"
---
# <a name="azure-enterprise-rest-apis"></a>Azure Enterprise REST API

本文介绍可用于 Azure 企业注册的 REST API。 此外还介绍了如何解决 REST API 的常见问题。

## <a name="consumption-and-usage-apis"></a>消耗量和使用量 API

Microsoft 企业 Azure 客户可以通过 REST API 获取使用情况和计费信息。 角色所有者（企业管理员、部门管理员、帐户所有者）必须通过从 Azure EA 门户生成一个密钥来启用对 API 的访问。 然后，获得了注册编号和密钥的任何人都可以通过 API 访问数据。

### <a name="available-apis"></a>可用 API

**余额和摘要** - [余额和摘要 API](../../billing/billing-enterprise-api-balance-summary.md) 提供关于余额、新购买、Azure 市场服务费用、调整和超额费用信息的每月摘要。 有关详细信息，请参阅[适用于企业客户的报告 API - 余额和摘要](/rest/api/billing/enterprise/billing-enterprise-api-balance-summary)。

**使用情况详细信息** - [使用情况详细信息 API](../../billing/billing-enterprise-api-usage-detail.md) 提供消耗数量和注册估计费用的日常明细。 结果还包括有关实例、计量和部门信息。 可以按照计费周期或指定的开始日期和结束日期查询 API。 有关详细信息，请参阅[适用于企业客户的报告 API - 使用情况详细信息](/rest/api/billing/enterprise/billing-enterprise-api-usage-detail)。

**市场应用商店费用** - [市场应用商店费用 API](../../billing/billing-enterprise-api-marketplace-storecharge.md) 按指定计费周期的天或开始和结束日期返回基于使用情况的市场费用明细。 有关详细信息，请参阅[适用于企业客户的报告 API - 市场应用商店费用](/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge)。

**价目表** - [价目表 API](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 为注册和计费周期的每个计量提供适用的费率。 有关详细信息，请参阅[适用于企业客户的报告 API - 价目表](/rest/api/billing/enterprise/billing-enterprise-api-pricesheet)。

**计费周期** - [计费周期 API](../../billing/billing-enterprise-api-billing-periods.md) 以倒序顺序为注册返回具有消耗数据的计费周期列表。 每个周期都有一个属性指向以下四个数据集的 API 路由：BalanceSummary、UsageDetails、Marketplace Charge 和 PriceSheet。 有关详细信息，请参阅[适用于企业客户的报告 API - 计费周期](/rest/api/billing/enterprise/billing-enterprise-api-billing-periods)。

### <a name="enable-api-data-access"></a>启用 API 数据访问

角色所有者可在 Azure EA 门户中执行以下步骤。 导航到“报告” > “下载使用情况” > “API 访问密钥”。    然后，他们可以：

- 生成主要和辅助访问密钥。
- 禁用访问密钥。
- 查看访问密钥的开始日期和结束日期。

### <a name="generate-or-retrieve-the-api-key"></a>生成或检索 API 密钥

1. 以企业管理员的身份登录。
2. 在左侧导航窗口中单击“报告”，然后单击“下载使用情况”选项卡。  
3. 单击“API 访问密钥”。 
4. 在“注册访问密钥”下，选择“生成密钥”符号生成主要或辅助密钥。 
5. 选择“展开密钥”以查看生成的整个 API 访问密钥。 
6. 选择“复制”获取立即可供使用的 API 访问密钥。 

![显示“API 访问密钥”页的示例](./media/ea-portal-rest-apis/ea-create-generate-or-retrieve-the-api-key.png)

若要为注册中的非企业管理员用户提供 API 访问密钥，请执行以下步骤：

1. 在左侧导航窗口中单击“管理”。 
2. 单击“DA 查看费用”（部门管理员查看费用）旁边的铅笔符号。 
3. 选择“启用”，然后单击“保存”。  
4. 单击“AO 查看费用”（帐户所有者查看费用）旁边的铅笔符号。 
5. 选择“启用”，然后单击“保存”。  

![显示已启用 DA 和 AO 查看费用的示例](./media/ea-portal-rest-apis/create-ea-generate-or-retrieve-api-key-enable-ao-do-view.png)上述步骤为 API 访问密钥持有者授予对使用情况报告中的成本和定价信息的访问权限。

### <a name="pass-keys-in-the-api"></a>在 API 中传递密钥

在身份验证和授权的每次调用中传递 API 密钥。 将以下属性传递到 HTTP 标头：

| 请求标头密钥 | 值 |
| --- | --- |
| 授权 | 使用以下格式指定该值：**bearer {API\_KEY}**
例如：bearer \&lt;APIKey\&gt; |

### <a name="swagger"></a>Swagger

[企业报告 v3 API](https://consumption.azure.com/swagger/ui/index) 中的以下 API 提供 Swagger 终结点。 Swagger 可帮助检查 API。 使用 Swagger 可以通过 [AutoRest](https://github.com/Azure/AutoRest) 或 [Swagger CodeGen](https://swagger.io/swagger-codegen/) 生成客户端 SDK。 可以通过 API 使用 2014 年 5 月 1 日之后提供的数据。

### <a name="api-response-codes"></a>API 响应代码

使用 API 时，会显示响应状态代码。 下表描述了这些代码。

| 响应状态代码 | 消息 | 说明 |
| --- | --- | --- |
| 200 | OK | 无错误 |
| 401 | 未授权 | API 密钥找不到、无效、已过期等。 |
| 404 | 不可用 | 找不到报表终结点 |
| 400 | 错误的请求 | 参数无效 – 日期范围、EA 号等。 |
| 500 | 服务器错误 | 处理请求时出现异常错误 |

### <a name="usage-and-billing-data-update-frequency"></a>使用情况和计费数据更新频率

使用情况和计费数据文件将在当前计费月份每隔 24 小时更新。 但是，可能会出现长达 3 天的数据延迟。 例如，如果在星期一使用了服务，数据可能会直到星期四才出现在数据文件中。

### <a name="test-enrollment-for-development"></a>用于开发的测试注册

没有 Azure 企业注册并想要访问 API 的合作伙伴或开发人员可以使用测试注册。 注册名称为 _EnrollmentNumber 100_，可以查找并测试直到 2018 年 6 月的使用信息。 然后，可以使用以下密钥调用 API 并查看示例数据。

```
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImpoeXA2UU9DWlZmY1pmdmhDVGR1OFdxeTJ5byJ9.eyJFbnJvbGxtZW50TnVtYmVyIjoiMTAwIiwiSWQiOiI1ZTc2ZmNiMy0xN2I4LTQ5ZDItYjdkOC0zMDU0YjUwOWY0MWYiLCJSZXBvcnRWaWV3IjoiU3lzdGVtIiwiUGFydG5lcklkIjoiIiwiRGVwYXJ0bWVudElkIjoiIiwiQWNjb3VudElkIjoiIiwiaXNzIjoiZWEubWljcm9zb2Z0YXp1cmUuY29tIiwiYXVkIjoiY2xpZW50LmVhLm1pY3Jvc29mdGF6dXJlLmNvbSIsImV4cCI6MTU4NjM5MDA2OSwibmJmIjoxNTcwNTc4ODY5fQ.lENR5pCBph6iZCVexUlN1b-j7StaILCyBewVHoILD-_fn8S2o2bHY1qUseGOkBwNlaFQfk2OZIo-jQYvnf3eP3UNrNVTCINT0APbc1RqgwSjZSxugVVHH9jnSzEjONkJaSKmi4tlidk6zkF1-uY-TPJkKxYN_9ar7BgLshF9JGXk7t8OZhxSCxDZc-smntu6ORFDl4gRZZVBKXhqOGjOAdYX5tPiGDF2Bxb68RSzh9Xyr5PXxKLx5yivZzUdo0-GFHo13V9w6a5VQM4R1w4_ro8jF8WAo3mpGZ_ovx_U5IY6zMNmi_AoA1mUyvTGotgcu94RragutoJRxAGHbNJZ0Q
```

### <a name="azure-service-catalog"></a>Azure 服务目录

所有 Azure 服务将以 CSV 格式发布到 Azure 存储博客中的某个目录。 如果需要为系统的所有 Azure 服务生成组织有序的目录，可以使用该目录。 当前目录位于 [https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv](https://azurecatalog.blob.core.windows.net/catalog/AzureCatalog.csv)。

### <a name="csv-data-file-details"></a>CSV 数据文件详细信息

以下信息描述 API 报告的属性。

#### <a name="usage-summary"></a>使用情况摘要

将从 CSV 报告生成 JSON 格式。 因此，格式与摘要 CSV 格式相同。 列名称受控制，因此，在使用 JSON 摘要数据时，应反序列化为数据表。

| CSV 列名 | JSON 列名 | JSON 新列 | 注释 |
| --- | --- | --- | --- |
| AccountOwnerId | AccountOwnerLiveId | AccountOwnerLiveId |   |
| 帐户名 | AccountName | AccountName |   |
| ServiceAdministratorId | ServiceAdministratorLiveId | ServiceAdministratorLiveId |   |
| SubscriptionId | SubscriptionId | SubscriptionId |   |
| SubscriptionGuid | MOCPSubscriptionGuid | SubscriptionGuid |   |
| 订阅名称 | SubscriptionName | SubscriptionName |   |
| Date | Date | Date | 显示服务目录报告的运行日期。 格式是不带时间戳的日期字符串。 |
| 月份 | 月份 | 月份 |   |
| 日期 | 日期 | 日期 |   |
| 年龄 | 年龄 | 年龄 |   |
| Products | BillableItemName | Products |   |
| 测定仪 ID | ResourceGUID | 计量 ID |   |
| 测定仪类别 | 服务 | MeterCategory | 有助于查找服务。 与具有多个 ServiceType 的服务相关。 例如“虚拟机”。 |
| 测定仪子类别 | ServiceType | MeterSubCategory | 提供服务的另一种详细级别。 例如“A1 VM (非 Windows)”。  |
| 测定仪区域 | ServiceRegion | MeterRegion | 服务所需的第三种详细级别。 查找 ResourceGUID 的区域上下文时非常有用。 |
| 测定仪名称 | ServiceResource | MeterName | 服务的名称。 |
| 已耗用数量 | ResourceQtyConsumed | ConsumedQuantity |   |
| ResourceRate | ResourceRate | ResourceRate |   |
| ExtendedCost | ExtendedCost | ExtendedCost |   |
| 资源位置 | ServiceSubRegion | ResourceLocation |   |
| 已耗用的服务 | ServiceInfo | ConsumedService |   |
| 实例 ID | 组件 | InstanceId |   |
| ServiceInfo1 | ServiceInfo1 | ServiceInfo1 |   |
| ServiceInfo2 | ServiceInfo2 | ServiceInfo2 |   |
| AdditionalInfo | AdditionalInfo | AdditionalInfo |   |
| Tags | Tags | Tags |   |
| 应用商店服务标识符   | OrderNumber | StoreServiceIdentifier   |   |
| 部门名称 | DepartmentName | DepartmentName |   |
| 成本中心 | CostCenter | CostCenter |   |
| 计量单位 | UnitOfMeasure | UnitOfMeasure | 示例值：小时、GB、事件、推送、单位、单位小时、MB、日单位 |
| ResourceGroup | ResourceGroup | ResourceGroup |   |

#### <a name="azure-marketplace-report"></a>Azure 市场报告

| CSV 列名 | JSON 列名 | JSON 新列 |
| --- | --- | --- |
| AccountOwnerId | AccountOwnerId | AccountOwnerId |
| 帐户名 | AccountName | AccountName |
| SubscriptionId | SubscriptionId | SubscriptionId |
| SubscriptionGuid | SubscriptionGuid | SubscriptionGuid |
| 订阅名称 | SubscriptionName |  SubscriptionName |
| Date | BillingCycle |  日期（仅限日期字符串。 不带时间戳）
| 月份 | 月份 |  月份 |
| 日期 | 日期 |  日期 |
| 年龄 | 年龄 |  年龄 |
| 测定仪 ID | MeterResourceId |  计量 ID |
| 发布者名称 | PublisherFriendlyName |  PublisherName |
| 产品名称 | OfferFriendlyName |  OfferName |
| 计划名称 | PlanFriendlyName |  PlanName |
| 已耗用数量 | BilledQty |  ConsumedQuantity |
| ResourceRate | ResourceRate | ResourceRate |
| ExtendedCost | ExtendedCost | ExtendedCost |
| 计量单位 | UnitOfMeasure | UnitOfMeasure |
| 实例 ID | InstanceId | InstanceId |
| 其他信息 | AdditionalInfo | AdditionalInfo |
| Tags | Tags | Tags |
| 订单编号 | OrderNumber | OrderNumber |
| 部门名称 | DepartmentNames | DepartmentName |
| 成本中心 | CostCenters |  CostCenter |
| 资源组 | ResourceGroup |  ResourceGroup |

#### <a name="price-sheet"></a>价目表

| CSV 列名 | JSON 列名 | 注释 |
| --- | --- | --- |
| 服务 | 服务 |  价格无变化 |
| 计量单位 | UnitOfMeasure |   |
| 超额部件号 | ConsumptionPartNumber |   |
| 超额单价 | ConsumptionPrice |   |
| 货币代码 | CurrencyCode |     |

### <a name="common-api-issues"></a>常见 API 问题

使用 Azure 企业 REST API 时，可能会遇到以下任一常见问题。

可能尝试使用了授权类型不正确的 API 密钥。 API 密钥由以下角色生成：

- 企业管理员
- 部门管理员 (DA)
- 帐户所有者 (AO)

EA 管理员生成的密钥可用于访问该注册的所有信息。 只读 EA 管理员无法生成 API 密钥。

DA 或 AO 生成的密钥不可用于访问余额、费用和价目表信息。

API 密钥每隔六个月过期。 如果已过期，则需要重新生成密钥。

如果收到超时错误，可以通过增大超时阈值限制来解决错误。

你可能会收到 401（未授权）过期错误。 此错误通常是密钥过期造成的。 如果密钥已过期，可以重新生成它。

如果所选日期范围内没有当前可用的数据，API 调用可能会返回 400 和 404（不可用）错误。 例如，发生此错误的原因可能是最近启动了注册转移。 特定日期和之后的数据现在会驻留在新注册中。 否则，该错误的原因可能是使用了新注册编号来检索驻留在旧注册中的信息。

## <a name="next-steps"></a>后续步骤

- Azure EA 门户管理员应阅读 [Azure EA portal administration](ea-portal-administration.md)（Azure EA 门户管理）来了解常见的管理任务。
- 在排查 Azure EA 门户问题时如需帮助，请参阅[排查 Azure EA 门户访问问题](ea-portal-troubleshoot.md)。
