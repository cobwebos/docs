---
title: 了解 Azure 成本管理数据 | Microsoft Docs
description: 本文帮助你更好地了解 Azure 成本管理中包含的数据、这些数据的处理频率以及收集、显示和关闭方式。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/20/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: f4c296aeefacc9516303ad75dd8b7d67325e38ee
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "65969054"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

本文帮助你更好地了解 Azure 成本管理中包含的数据。 此外，介绍这些数据的处理频率以及收集、显示和关闭方式。 你需要按月支付 Azure 使用费。 但是，月底的账单由 Azure 订阅类型决定。 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

成本的管理包括所有使用情况和购买，包括保留项和第三方产品/服务的企业协议 (EA) 帐户。 Microsoft 客户协议 (MCA) 帐户和即用即付订阅仅包含从 Azure 和 Marketplace 服务的使用情况。 支持和其他成本不包括。 直到生成发票估计成本未纳入的信用额度和。

## <a name="supported-microsoft-offers"></a>支持的 Microsoft 套餐

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。  Azure 套餐是客户拥有的 Azure 订阅类型。

| 类别  | **产品/服务名称** | **Quota ID** | **套餐编号** |
| --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P |
| **企业协议 (EA)** | Enterprise 开发/测试                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P |
| **Microsoft 客户协议** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 不适用 |
| **Microsoft 客户协议** | [Microsoft Azure 开发/测试计划](https://azure.microsoft.com/offers/ms-azr-0148g)  | MSDNDevTest_2014-09-01 | 不适用 |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p) | MSDN_2014-09-01 | MS-AZR-0062P |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)                       | PayAsYouGo_2014-09-01 | MS-AZR-0003P |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p)              | MSDNDevTest_2014-09-01 | MS-AZR-0023P |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p)           | MPN_2014-09-01 | MS-AZR-0025P |
| **即用即付** | [免费试用](https://azure.microsoft.com/offers/ms-azr-0044p)                          | FreeTrial_2014-09-01 | MS-AZR-0044P |
| **即用即付** | [Azure 开放式许可](https://azure.microsoft.com/offers/ms-azr-0111p)                       | AzureInOpen_2014-09-01 | MS-AZR-0111P |
| **即用即付** | [面向学生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p)                  | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **即用即付** | Azure Pass                                                                             | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)      | MPN_2014-09-01 | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)          | MSDN_2014-09-01 | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)     | MSDNDevTest_2014-09-01 | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)            | MSDN_2014-09-01 | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)  | MSDN_2014-09-01 | MS-AZR-0064P |

下表显示了不支持的套餐。

| 类别  | **产品/服务名称** | **Quota ID** | **套餐编号** |
| --- | --- | --- | --- |
| **云解决方案提供商 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **云解决方案提供商 (CSP)** | Azure 政府版 CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **云解决方案提供商 (CSP)** | Microsoft 德国云 CSP 中的 Azure 德国版   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **即用即付**                 | 面向学生的 Azure 入门版 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **即用即付**                 | [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支持计划** | 标准支持                    | Default_2014-09-01 | MS-AZR-0041P |
| **支持计划** | 专业直接支持         | Default_2014-09-01 | MS-AZR-0042P |
| **支持计划** | 开发人员支持                   | Default_2014-09-01 | MS-AZR-0043P |
| **支持计划** | 德国支持计划                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支持计划** | Azure 政府版标准支持   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支持计划** | Azure 政府版专业直接支持 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支持计划** | Azure 政府版开发人员支持  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

对于使用即用即付、MSDN 和 Visual Studio 产品/服务类别的客户，从 2018 年 2 月 10 日起可在“成本管理”中使用数据。 若要访问的数据，你在 2018 年 10 月 02 日之前的订阅，可以使用[Azure 帐户中心](https://account.azure.com/subscriptions)若要下载你的使用情况详细信息的 CSV 文件中也可以使用[使用情况详细信息 API](/rest/api/consumption/usagedetails)。

## <a name="determine-your-offer-type"></a>确定你的产品/服务类型
如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”  。 在服务列表中选择“订阅”。  在订阅列表菜单中，单击要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。   下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。 所有成本都估计直到生成发票。 显示成本不包括免费版和预付信用额度。

**成本和用量数据**

| **包括** | **不包括** |
| --- | --- |
| Azure 服务用量<sup>1</sup> | 预订 – 有关详细信息，请参阅 [Azure 预订自动化的 API](../billing/billing-reservation-apis.md)。 |
| Marketplace 产品/服务使用情况<sup>2</sup> | 市场购买 – 有关详细信息，请参阅[第三方服务费用](../billing/billing-understand-your-azure-marketplace-charges.md)。 |
|   | 支持费用 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
|   | 税费 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
|   | 信用额度 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |

<sup>1</sup> Azure 服务用量基于预订价格和议价。

<sup>2</sup> marketplace 产品/服务使用情况不可用于即用即付、 MSDN 和 Visual Studio 提供了这一次。

**元数据**

| **包括** | **不包括** |
| --- | --- |
| 资源标记<sup>3</sup> | 资源组标记 |

<sup>3</sup>资源标记应用从每个服务发出使用情况和不可用于以追溯方式进行的历史使用情况。

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

无论是使用[成本管理 Api](https://aka.ms/costmgmt/docs)，Power BI 或 Azure 门户来检索数据，希望重新获取已分级，并因此更改，直到关闭发票的当前计费周期的费用。

## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="historical-data-might-not-match-invoice"></a>历史数据可能与发票不匹配

对于基于信用额度和提前支付产品/服务的历史数据可能与你的发票。 某些 Azure 即用即付、 MSDN 以及 Visual Studio 产品/服务可以具有的 Azure 额度和高级应用于发票的付款。 但是，在成本管理中所示的历史数据基于您估计耗用量费用。 成本的管理历史数据不包括付款和信用额度。 结果是，针对以下产品/服务显示的历史数据可能不与你的发票完全匹配。

-   MS-AZR-0029P
-   MS-AZR-0064P
-   MS-AZR-0170P
-   MS-AZR-0062P
-   MS-AZR-0123P
-   MS-AZR-0129P
-   MS-AZR-0044P
-   MS-AZR-0128P
-   MS-AZR-0120P
-   MS-AZR-0125P
-   MS-AZR-0059P
-   MS-AZR-0063P
-   MS-AZR-0060P
-   MS-AZR-0111P

## <a name="see-also"></a>另请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
