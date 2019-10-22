---
title: 跟踪 Microsoft 客户协议的 Azure 额度余额
description: 了解如何查看 Microsoft 客户协议的 Azure 额度余额。
author: bandersmsft
manager: amberb
tags: billing
ms.service: billing
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/01/2019
ms.author: banders
ms.openlocfilehash: 4eae7299ab696b01c57a27fd46cbf903c9395152
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72375533"
---
# <a name="track-microsoft-customer-agreement-azure-credit-balance"></a>跟踪 Microsoft 客户协议的 Azure 额度余额

可以在 Azure 门户中查看 Microsoft 客户协议的计费帐户的 Azure 额度余额。 

使用额度来支付符合额度要求的费用。 如果你使用的产品不符合额度要求，或者使用量超出额度余额，我们会对你收费。 有关详细信息，请参阅 [Azure 额度未涵盖的产品](#products-that-arent-covered-by-azure-credits)。

在 Microsoft 客户协议的计费帐户中，额度分配给计费对象信息。 每个计费对象信息都有其自己的额度。 若要查看计费对象信息的 Azure 额度余额，你必须在计费对象信息上有所有者、参与者、读者或发票管理者角色，或者必须在计费帐户上有所有者、参与者或读者角色。 若要详细了解这些角色，请参阅[了解 Azure 中的 Microsoft 客户协议管理角色](billing-understand-mca-roles.md)。

本文适用于 Microsoft 客户协议的计费帐户。 [检查你是否有权访问 Microsoft 客户协议](#check-access-to-a-microsoft-customer-agreement)。

## <a name="check-your-credit-balance-in-the-azure-portal"></a>在 Azure 门户中检查额度余额

1. 登录到 [Azure 门户]( https://portal.azure.com)。

2. 搜索“成本管理 + 计费”。 

    ![显示在门户中搜索“成本管理 + 计费”的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-search-cost-management-billing.png)

3.  在左侧选择“Azure 额度”。  根据你的访问权限，可能需要选择计费帐户或计费对象信息，然后选择“Azure 额度”  。

4. Azure 额度页显示以下信息：

   ![计费对象信息的额度余额和交易的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-overview.png)

   | 术语               | 定义                           |
   |--------------------|--------------------------------------------------------|
   | 估计余额  | 在考虑所有计费交易和挂起交易后估算出的额度金额 |
   | 当前余额    | 自上次发票以来的额度金额。 它不包括任何挂起交易。 |
   | 事务       | 受 Azure 额度余额影响的计费交易 |

   当预估的余额降到 0 时，所有使用量都收费，包括对符合额度要求的产品收费。

6. 选择“额度列表”，查看计费对象信息的额度的列表。  额度列表提供以下信息：

   ![计费对象信息的额度列表的屏幕截图](./media/billing-mca-check-azure-credits-balance/billing-mca-credits-list.png)

   | 术语 | 定义 |
   |---|---|
   | 源 | 额度的获取源 |
   | 开始日期 | 获得额度的日期 |
   | 到期日期 | 额度过期的日期 |
   | 当前余额 | 自上次发票以来的余额 |
   | 原始金额 | 原始额度金额 |
   | 状态 | 额度的当前状态。 状态可能为活动、已使用、已过期或将要过期 |

## <a name="check-your-credit-balance-programmatically"></a>以编程方式查看额度余额

可以使用 [Azure 计费](https://docs.microsoft.com/rest/api/billing/)和[消耗](https://docs.microsoft.com/rest/api/consumption/) API 以编程方式获取计费帐户的额度余额。

下面显示的示例使用 REST API。 目前，PowerShell 和 Azure CLI 不受支持。

### <a name="find-billing-profiles-you-have-access-to"></a>查找有权访问的计费对象信息

```json
GET https://management.azure.com/providers/Microsoft.Billing/billingAccounts?$expand=billingProfiles&api-version=2019-10-01-preview
```
API 响应返回一个列表，其中包含计费帐户及其计费对象信息。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "name": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx",
      "properties": {
        "accountId": "5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
        "accountStatus": "Active",
        "accountType": "Enterprise",
        "agreementType": "MicrosoftCustomerAgreement",
        "billingProfiles": [
          {
            "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx",
            "name": "PBFV-xxxx-xxx-xxx",
            "properties": {
              "address": {
                "addressLine1": "AddressLine1",
                "city": "City",
                "companyName": "CompanyName",
                "country": "Country",
                "postalCode": "xxxxx",
                "region": "Region"
              },
              "currency": "USD",
              "displayName": "Development",
              "hasReadAccess": true,
              "invoiceDay": 5,
              "invoiceEmailOptIn": true
            },
            "type": "Microsoft.Billing/billingAccounts/billingProfiles"
          }
        ],
        "displayName": "Contoso",
        "hasReadAccess": true,
      },
      "type": "Microsoft.Billing/billingAccounts"
    }
  ]
}
```

使用计费对象信息的 `displayName` 属性来标识需要检查其额度余额的计费对象信息。 复制计费对象信息的 `id`。 例如，如果需要检查“开发”  计费对象信息的额度余额，可复制 ```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```。 将该值粘贴到某个位置，以便在下一步使用它。

### <a name="get-azure-credit-balance"></a>获取 Azure 额度余额 

发出以下请求，将 `<billingProfileId>` 替换为从第一步复制的 `id` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/credits/balanceSummary?api-version=2019-10-01
```

API 响应返回计费对象信息的估算余额和当前余额。

```json
{
  "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/credits/balanceSummary/57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "name": "57c2e8df-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.Consumption/credits/balanceSummary",
  "eTag": null,
  "properties": {
    "balanceSummary": {
      "estimatedBalance": {
        "currency": "USD",
        "value": 996.13
      },
      "currentBalance": {
        "currency": "USD",
        "value": 997.87
      }
    },
    "pendingCreditAdjustments": {
      "currency": "USD",
      "value": 0.0
    },
    "expiredCredit": {
      "currency": "USD",
      "value": 0.0
    },
    "pendingEligibleCharges": {
      "currency": "USD",
      "value": -1.74
    }
  }
}
```

| 元素名称  | 说明                                                                           |
|---------------|---------------------------------------------------------------------------------------|
| `estimatedBalance` | 在考虑所有计费交易和挂起交易后估算出的额度金额。 |
| `currentBalance`   | 自上次发票以来的额度金额。 它不包括任何挂起交易。    |
| `pendingCreditAdjustments`      | 尚未开票的调整项（例如退款）。  |
| `expiredCredit`      |  自上次发票以来已过期的额度。  |
| `pendingEligibleCharges`  | 尚未开票的符合额度要求的费用。   |

### <a name="get-list-of-credits"></a>获取额度列表

发出以下请求，将 `<billingProfileId>` 替换为从第一步复制的 `id` (```/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/lots?api-version=2019-10-01
```
API 响应返回计费对象信息的 Azure 额度列表。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "f2ecfd94-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 500.0
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/xxxx-xxxx-xxx-xxx/providers/Microsoft.Consumption/lots/4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4ea40eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/lots",
      "eTag": null,
      "properties": {
        "originalAmount": {
          "currency": "USD",
          "value": 500.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 497.87
        },
        "source": "Azure Promotional Credit",
        "startDate": "09/18/2019 21:47:31",
        "expirationDate": "09/18/2020 21:47:30",
        "poNumber": ""
      }
    }
  ]
}
```
| 元素名称  | 说明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `originalAmount` | 原始额度金额。 |
| `closedBalance`   | 自上次发票以来的余额。    |
| `source`      | 源，用于定义从何处获得了额度。 |
| `startDate`      |  额度激活的日期。  |
| `expirationDate`  | 额度过期的日期。   |
| `poNumber`  | 在其上计入了额度的发票的采购订单号。   |

### <a name="get-transactions-that-affected-credit-balance"></a>获取影响了额度余额的交易

发出以下请求，将 `<billingProfileId>` 替换为从第一步复制的 `id` (```providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx```)。 需传递 **startDate** 和 **endDate** 才能获取所需期间的交易。

```json
GET https://management.azure.com<billingProfileId>/providers/Microsoft.Consumption/events?api-version=2019-10-01&startDate=2018-10-01T00:00:00.000Z&endDate=2019-10-11T12:00:00.000Z?api-version=2019-10-01
```
API 响应返回影响了计费对象信息的额度余额的所有交易。

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx`/providers/Microsoft.Consumption/events/e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "e2032eb5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "10/11/2019",
        "description": "Credit eligible charges as of 10/11/2019",
        "newCredit": {
          "currency": "USD",
          "value": 0.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": -1.74
        },
        "closedBalance": {
          "currency": "USD",
          "value": 998.26
        },
        "eventType": "PendingCharges",
        "invoiceNumber": ""
      }
    },
    {
      "id": "/providers/Microsoft.Billing/billingAccounts/5e98e158-xxxx-xxxx-xxxx-xxxxxxxxxxxx:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx_xxxx-xx-xx/billingProfiles/PBFV-xxxx-xxx-xxx/providers/Microsoft.Consumption/events/381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "381efd80-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Consumption/events",
      "eTag": null,
      "properties": {
        "transactionDate": "09/18/2019",
        "description": "New credit added on 09/18/2019",
        "newCredit": {
          "currency": "USD",
          "value": 500.0
        },
        "adjustments": {
          "currency": "USD",
          "value": 0.0
        },
        "creditExpired": {
          "currency": "USD",
          "value": 0.0
        },
        "charges": {
          "currency": "USD",
          "value": 0.0
        },
        "closedBalance": {
          "currency": "USD",
          "value": 1000.0
        },
        "eventType": "PendingNewCredit",
        "invoiceNumber": ""
      }
    }
  ]
}
```
| 元素名称  | 说明                                                                                               |
|---------------|-----------------------------------------------------------------------------------------------------------|
| `transactionDate` | 交易发生日期。 |
| `description` | 交易说明。 |
| `adjustments`   | 针对交易的额度调整。    |
| `creditExpired`      | 过期的额度金额。 |
| `charges`      |  针对交易的收费。  |
| `closedBalance`  | 交易后的余额。   |
| `eventType`  | 交易类型。   |
| `invoiceNumber`  | 在其上计收交易费的发票的发票号。 对于挂起的交易，它将为空。   |

## <a name="how-credits-are-used"></a>额度使用方式

在 Microsoft 客户协议的计费帐户中，请使用计费对象信息来管理发票和付款方式。 将为每个计费对象信息生成每月发票，你可以使用付款方式为发票付款。

将获得的额度分配给计费对象信息。 为计费对象信息生成发票时，额度自动应用到总费用，这样就能计算出你需要支付的金额。 使用付款方式（例如支票/电汇或信用卡）支付剩余金额。

## <a name="products-that-arent-covered-by-azure-credits"></a>Azure 额度不涵盖的产品

 Azure 额度不涵盖以下产品。 无论你的额度余额是多少，使用这些产品都需付费。

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop
- 已注册的用户
- Openlogic
- 远程访问权限 XenApp Essentials 已注册的用户
- Ubuntu Advantage
- Visual Studio Enterprise（每月）
- Visual Studio Enterprise（每年）
- Visual Studio Professional（每月）
- Visual Studio Professional（每年）
- Azure 市场产品
- Azure 支持计划

## <a name="check-access-to-a-microsoft-customer-agreement"></a>检查对 Microsoft 客户协议的访问权限
[!INCLUDE [billing-check-mca](../../includes/billing-check-mca.md)]

## <a name="need-help-contact-support"></a>需要帮助？ 联系支持人员。

如果需要帮助，请[联系支持人员](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)以快速解决问题。

## <a name="next-steps"></a>后续步骤

- [了解 Microsoft 客户协议的计费帐户](billing-mca-overview.md)
- [了解 Microsoft 客户协议发票中的术语](billing-mca-understand-your-invoice.md)
