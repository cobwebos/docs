---
title: 了解 Azure 成本管理数据 | Microsoft Docs
description: This article helps you better understand data that's included in Azure Cost Management and how frequently it's processed, collected, shown, and closed.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 11/13/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: micflan
ms.custom: ''
ms.openlocfilehash: 25a20ca40b517209fca7b969e3a7399a1c7c7387
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229759"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

This article helps you better understand Azure cost and usage data that's included in Azure Cost Management. It explains how frequently data is processed, collected, shown, and closed. 你需要按月支付 Azure 使用费。 Although billing cycles are monthly periods, cycle start and end dates vary by subscription type. 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

Cost Management includes all usage and purchases, including reservations and third-party offerings for Enterprise Agreement (EA) accounts. Microsoft Customer Agreement accounts and individual subscriptions with pay-as-you-go rates  only include usage from Azure and Marketplace services. Support and other costs are not included. Costs are estimated until an invoice is generated and do not factor in credits.

## <a name="supported-microsoft-azure-offers"></a>Supported Microsoft Azure offers

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 套餐是客户拥有的 Azure 订阅类型。 Data is available in Cost Management starting on the **Data available from** date. If a subscription changes offers, costs before the offer change date will not be available.

| **类别**  | **产品/服务名称** | **Quota ID** | **套餐编号** | **Data available from** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | May 2014<sup>1</sup> |
| **企业协议 (EA)** | 企业开发/测试                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | May 2014<sup>1</sup> |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | May 2014<sup>1</sup> |
| **Microsoft 客户协议** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | N/A | March 2019<sup>3</sup> |
| **Microsoft 客户协议** | [Microsoft Azure Plan for Dev/Test](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | N/A | March 2019<sup>3</sup> |
| **Microsoft Customer Agreement supported by partners** | Microsoft Azure 计划 | CSP_2015-05-01, CSP_MG_2017-12-01, and CSPDEVTEST_2018-05-01<br><br>The quota ID is reused for Microsoft Customer Agreement and legacy CSP subscriptions. Currently, only Microsoft Customer Agreement subscriptions are supported. | N/A | 2019 年 10 月 |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN Platforms](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | October 2, 2018<sup>2</sup> |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | October 2, 2018<sup>2</sup> |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | October 2, 2018<sup>2</sup> |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | October 2, 2018<sup>2</sup> |
| **即用即付** | [Free Trial](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | October 2, 2018<sup>2</sup> |
| **即用即付** | [Azure in Open](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | October 2, 2018<sup>2</sup> |
| **即用即付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | October 2, 2018<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise: BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | October 2, 2018<sup>2</sup> |

_<sup>**1**</sup> For data before May 2014, visit the [Azure Enterprise portal](https://ea.azure.com)._

_<sup>**2**</sup> For data before October 2, 2018, visit the [Azure Account Center](https://account.azure.com/subscriptions)._

_<sup>**3**</sup> Microsoft Customer Agreements started in March 2019 and do not have any historical data before this point._

_<sup>**4**</sup> Historical data for credit-based and pay-in-advance subscriptions might not match your invoice. See [Historical data may not match invoice](#historical-data-might-not-match-invoice) below._

The following offers are not supported yet:

| 类别  | **产品/服务名称** | **Quota ID** | **套餐编号** |
| --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure 政府即用即付 | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **云解决方案提供商 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **云解决方案提供商 (CSP)** | Azure 政府版 CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **云解决方案提供商 (CSP)** | Microsoft 云德国 CSP 中的 Azure Germany   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **即用即付**                 | Azure for Students Starter | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **即用即付** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **即用即付**                 | [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支持计划** | 标准支持                    | Default_2014-09-01 | MS-AZR-0041P |
| **支持计划** | 专业直接支持         | Default_2014-09-01 | MS-AZR-0042P |
| **支持计划** | 开发人员支持                   | Default_2014-09-01 | MS-AZR-0043P |
| **支持计划** | 德国支持计划                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支持计划** | Azure 政府版标准支持   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支持计划** | Azure 政府版专业直接支持 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支持计划** | Azure 政府版开发人员支持  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>Determine your offer type
如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”。 在服务列表中选择“订阅”。 在订阅列表菜单中，单击要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。 下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。 All costs are estimated until an invoice is generated. Costs shown do not include free and prepaid credits.

**成本和用量数据**

| **包括** | **不包括** |
| --- | --- |
| Azure service usage<sup>5</sup>        | 支持费用 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace offering usage<sup>6</sup> | 税费 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace purchases<sup>6</sup>      | 信用额度 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Reservation purchases<sup>7</sup>      |  |
| Amortization of reservation purchases<sup>7</sup>      |  |

_<sup>**5**</sup> Azure service usage is based on reservation and negotiated prices._

_<sup>**6**</sup> Marketplace purchases are not available for Pay-As-You-Go, MSDN, and Visual Studio offers at this time._

_<sup>**7**</sup> Reservation purchases are only available for Enterprise Agreement (EA) accounts at this time._

**元数据**

| **包括** | **不包括** |
| --- | --- |
| Resource tags<sup>8</sup> | 资源组标记 |

_<sup>**8**</sup> Resource tags are applied as usage is emitted from each service and aren't available retroactively to historical usage._

## <a name="rated-usage-data-refresh-schedule"></a>用量计费数据刷新计划

成本和用量数据在 Azure 门户上的“成本管理 + 计费”以及[支持 API](index.yml) 中提供。 查看成本时，请记住以下几点：

- 当前计费周期的估算费用每天更新六次。
- 由于用量会增大，当前计费周期的估算费用可能会更改。
- 每次更新都是累积性的，包括前次更新的所有细目和信息。
- Azure 在当前计费周期结束后的最多 72 小时（三个日历日）内终结或_关闭_该计费周期。

以下示例演示计费周期的结束方式。

企业协议 (EA) 订阅 – 如果计费月在 3 月 31 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 4 月 4 日午夜 (UTC)。

即用即付订阅 – 如果计费月在 5 月 15 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新计费数据

Whether you use the [Cost Management APIs](index.yml), Power BI, or the Azure portal to retrieve data, expect the current billing period's charges to get rerated, and consequently change, until the invoice is closed.

## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="historical-data-might-not-match-invoice"></a>Historical data might not match invoice

Historical data for credit-based and pay-in-advance offers might not match your invoice. Some Azure pay-as-you-go, MSDN, and Visual Studio offers can have Azure credits and advanced payments applied to the invoice. However, the historical data shown in Cost Management is based on your estimated consumption charges only. Cost Management historical data doesn't include payments and credits. As a result, the historical data shown for the following offers may not match exactly with your invoice.

- Azure for Students (MS-AZR-0170P)
- Azure in Open (MS-AZR-0111P)
- Azure Pass (MS-AZR-0120P, MS-AZR-0123P, MS-AZR-0125P, MS-AZR-0128P, MS-AZR-0129P)
- Free Trial (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-AZR-0029P, MS-AZR-0059P, MS-AZR-0060P, MS-AZR-0063P, MS-AZR-0064P)

## <a name="see-also"></a>另请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
