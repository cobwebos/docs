---
title: 了解 Azure 成本管理数据 | Microsoft Docs
description: 本文帮助你更好地了解 Azure 成本管理中包含的数据、这些数据的处理频率以及收集、显示和关闭方式。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/01/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 44b95c92f51ca9782fca492f3dec3142087ecc91
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/11/2019
ms.locfileid: "67797023"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

本文可帮助您更好地了解 Azure 成本管理中包含的 Azure 成本和用量数据。 它还说明了频率处理的数据收集，所示，和已关闭。 你需要按月支付 Azure 使用费。 虽然计费周期每月期间，周期的开始和结束日期因订阅类型。 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

成本的管理包括所有使用情况和购买，包括保留项和第三方产品/服务的企业协议 (EA) 帐户。 Microsoft 客户协议 (MCA) 帐户和即用即付费率使用单独的订阅仅包含从 Azure 和 Marketplace 服务的使用情况。 支持和其他成本不包括。 直到生成发票估计成本未纳入的信用额度和。

## <a name="supported-microsoft-azure-offers"></a>受支持的 Microsoft Azure 产品/服务

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 套餐是客户拥有的 Azure 订阅类型。 数据是可用的成本管理上启动**从可用数据**日期。 如果订阅更改产品/服务，产品/服务更改日期之前的成本将不可用。 

| **类别**  | **产品/服务名称** | **Quota ID** | **套餐编号** | **从可用数据** |
| --- | --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p)      | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P | 2018 年 10 月 2 日<sup>2</sup> |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 年 5 月<sup>1</sup> |
| **企业协议 (EA)** | Enterprise 开发/测试                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 年 5 月<sup>1</sup> |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 年 5 月<sup>1</sup> |
| **Microsoft 客户协议** | [Microsoft Azure Plan](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 不可用 | Mar 2019<sup>3</sup> |
| **Microsoft 客户协议** | [Microsoft Azure 开发/测试计划](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 不可用 | Mar 2019<sup>3</sup> |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [Azure for Students](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018 年 10 月 2 日<sup>2</sup> |

_<sup>**1** </sup> 2014 年 5 月之前的数据，请访问[Azure Enterprise 门户](https://ea.azure.com)。_

_<sup>**2** </sup>数据，再于 2018 年 10 月 2 日，请访问[Azure 帐户中心](https://account.azure.com/subscriptions)。_

_<sup>**3** </sup> Microsoft 客户协议在 2019 年 3 月开始，但没有在此点之前的任何历史数据。_

_<sup>**4** </sup>基于信用额度和提前付费订阅的历史数据可能与你的发票不匹配。请参阅[历史数据可能不匹配发票](#historical-data-might-not-match-invoice)下面。_

下表显示了其中尚不支持的产品/服务。

| 类别  | **产品/服务名称** | **Quota ID** | **套餐编号** |
| --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
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

## <a name="determine-your-offer-type"></a>确定你的产品/服务类型
如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”  。 在服务列表中选择“订阅”。  在订阅列表菜单中，单击要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。   下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。 所有成本都估计直到生成发票。 显示成本不包括免费版和预付信用额度。

**成本和用量数据**

| **包括** | **不包括** |
| --- | --- |
| Azure 服务使用情况<sup>5</sup>        | 支持费用 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 产品/服务使用情况<sup>6</sup> | 税费 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| Marketplace 购买内容<sup>6</sup>      | 信用额度 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
| 预订购买<sup>7</sup>      |  |

_<sup>**5** </sup> azure 服务使用情况取决于保留并协商的价格。_

_<sup>**6** </sup> marketplace 使用情况和购买不可用于即用即付、 MSDN 和 Visual Studio 提供了这一次。_

_<sup>**7** </sup>预订购买目前仅适用于企业协议 (EA) 帐户。_

**元数据**

| **包括** | **不包括** |
| --- | --- |
| 资源标记<sup>8</sup> | 资源组标记 |

_<sup>**8** </sup>资源标记应用从每个服务发出使用情况和不可用于以追溯方式进行的历史使用情况。_

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

无论是使用[成本管理 Api](index.yml)，Power BI 或 Azure 门户来检索数据，希望重新获取已分级，并因此更改，直到关闭发票的当前计费周期的费用。

## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="historical-data-might-not-match-invoice"></a>历史数据可能与发票不匹配

对于基于信用额度和提前支付产品/服务的历史数据可能与你的发票。 某些 Azure 即用即付、 MSDN 以及 Visual Studio 提供了可以具有的 Azure 额度和高级的付款应用于该发票。 但是，在成本管理中所示的历史数据基于您估计耗用量费用。 成本的管理历史数据不包括付款和信用额度。 结果是，针对以下产品/服务显示的历史数据可能不与你的发票完全匹配。

- Azure for Students (MS-条-0170 P)
- Azure 开放许可 (MS-条-0111 P)
- Azure Pass (MS-条-0120 P、 MS-条-0123 P、 MS-条-0125 P、 MS-条-0128 P、 MS-条-0129 P)
- 免费试用版 (MS-条-0044 P)
- MSDN (MS-AZR-0062P)
- Visual Studio (MS-条-0029 P、 MS-条-0059 P、 MS-条-0060 P、 MS-条-0063 P、 MS-条-0064 P)

## <a name="see-also"></a>请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
