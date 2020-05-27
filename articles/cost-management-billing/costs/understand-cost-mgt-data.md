---
title: 了解 Azure 成本管理数据
description: 本文帮助你更好地了解 Azure 成本管理中包含的数据、这些数据的处理频率以及收集、显示和关闭方式。
author: bandersmsft
ms.author: banders
ms.date: 03/02/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: micflan
ms.openlocfilehash: 5fce5c8de3b2224ef471b0b3eec5ff29a869a9f6
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2020
ms.locfileid: "83844516"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

本文帮助你更好地了解 Azure 成本管理中包含的 Azure 成本和使用情况数据。 本文介绍这些数据的处理频率以及收集、显示和关闭方式。 你需要按月支付 Azure 使用费。 尽管计费周期是按月的，但周期开始日期和结束日期因订阅类型而异。 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

成本管理包括所有使用情况和购买情况，其中包括企业协议 (EA) 帐户的预留和第三方产品/服务。 Microsoft 客户协议帐户和使用即用即付费率的个人订阅仅包含 Azure 和市场服务的使用情况。 不包括支持和其他成本。 在生成发票之前估计成本，而不考虑额度。

如果你有新订阅，则无法立即使用成本管理功能。 最多可能需要 48 小时才能使用所有成本管理功能。

## <a name="supported-microsoft-azure-offers"></a>支持的 Microsoft Azure 套餐

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。 Azure 套餐是客户拥有的 Azure 订阅类型。 从“数据可用”日期开始，成本管理中会提供数据。  如果订阅更改了套餐，则套餐更改日期之前的成本不可用。

| **类别**  | **产品/服务名称** | **配额 ID** | **套餐编号** | **数据可用** |
| --- | --- | --- | --- | --- |
| **Azure Government** | Azure Government Enterprise                                                         | EnterpriseAgreement_2014-09-01 | MS-AZR-USGOV-0017P | 2014 年 5 月<sup>1</sup> |
| **企业协议 (EA)** | Enterprise 开发/测试                                                        | MSDNDevTest_2014-09-01 | MS-AZR-0148P | 2014 年 5 月<sup>1</sup> |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade) | EnterpriseAgreement_2014-09-01 | MS-AZR-0017P | 2014 年 5 月<sup>1</sup> |
| **Microsoft 客户协议** | [Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0017g) | EnterpriseAgreement_2014-09-01 | 空值 | 2019 年 3 月<sup>3</sup> |
| **Microsoft 客户协议** | [适用于开发/测试的 Microsoft Azure 计划](https://azure.microsoft.com/offers/ms-azr-0148g) | MSDNDevTest_2014-09-01 | 空值 | 2019 年 3 月<sup>3</sup> |
| **合作伙伴支持的 Microsoft 客户协议** | Microsoft Azure 计划 | CSP_2015-05-01、CSP_MG_2017-12-01 和 CSPDEVTEST_2018-05-01<br><br>此配额 ID 可重复用于 Microsoft 客户协议和旧 CSP 订阅。 目前仅支持 Microsoft 客户协议订阅。 | 空值 | 2019 年 10 月 |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0062P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p)                  | PayAsYouGo_2014-09-01 | MS-AZR-0003P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p)         | MSDNDevTest_2014-09-01 | MS-AZR-0023P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p)      | MPN_2014-09-01 | MS-AZR-0025P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [免费试用版](https://azure.microsoft.com/offers/ms-azr-0044p)<sup>4</sup>         | FreeTrial_2014-09-01 | MS-AZR-0044P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | [Azure 开放许可](https://azure.microsoft.com/offers/ms-azr-0111p)<sup>4</sup>      | AzureInOpen_2014-09-01 | MS-AZR-0111P | 2018 年 10 月 2 日<sup>2</sup> |
| **即用即付** | Azure Pass<sup>4</sup>                                                            | AzurePass_2014-09-01 | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p)<sup>4</sup>     | MPN_2014-09-01 | MS-AZR-0029P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p)<sup>4</sup>         | MSDN_2014-09-01 | MS-AZR-0059P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p)<sup>4</sup>    | MSDNDevTest_2014-09-01 | MS-AZR-0060P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p)<sup>4</sup>           | MSDN_2014-09-01 | MS-AZR-0063P | 2018 年 10 月 2 日<sup>2</sup> |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p)<sup>4</sup> | MSDN_2014-09-01 | MS-AZR-0064P | 2018 年 10 月 2 日<sup>2</sup> |

<sup>**1**</sup> 对于 2014 年 5 月之前的数据，请访问 [Azure 企业门户](https://ea.azure.com)。 

<sup>**2**</sup> 对于 2018 年 10 月 2 日之前的数据，请访问 [Azure 帐户中心](https://account.azure.com/subscriptions)。 

<sup>**3**</sup> Microsoft 客户协议从 2019 年 3 月开始，在此时间点之前没有任何历史数据。 

<sup>**4**</sup> 基于额度的订阅和提前支付订阅的历史数据可能与发票不匹配。  请参阅下面的[历史数据可能与发票不匹配](#historical-data-might-not-match-invoice)。

目前尚不支持以下套餐：

| 类别  | **产品/服务名称** | **配额 ID** | **套餐编号** |
| --- | --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p) | PayAsYouGo_2014-09-01 | MS-AZR-DE-0003P |
| **Azure Government** | Azure 政府即用即付 | PayAsYouGo_2014-09-01 | MS-AZR-USGOV-0003P |
| **云解决方案提供商 (CSP)** | Microsoft Azure                                    | CSP_2015-05-01 | MS-AZR-0145P |
| **云解决方案提供商 (CSP)** | Azure 政府版 CSP                               | CSP_2015-05-01 | MS-AZR-USGOV-0145P |
| **云解决方案提供商 (CSP)** | Microsoft 德国云 CSP 中的 Azure 德国版   | CSP_2015-05-01 | MS-AZR-DE-0145P |
| **即用即付**                 | 面向学生的 Azure 入门版 | DreamSpark_2015-02-01 | MS-AZR-0144P |
| **即用即付** | [面向学生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p)<sup>4</sup> | AzureForStudents_2018-01-01 | MS-AZR-0170P |
| **即用即付**                 | [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/) | Sponsored_2016-01-01 | MS-AZR-0036P |
| **支持计划** | 标准支持                    | Default_2014-09-01 | MS-AZR-0041P |
| **支持计划** | 专业直接支持         | Default_2014-09-01 | MS-AZR-0042P |
| **支持计划** | 开发人员支持                   | Default_2014-09-01 | MS-AZR-0043P |
| **支持计划** | 德国支持计划                | Default_2014-09-01 | MS-AZR-DE-0043P |
| **支持计划** | Azure 政府版标准支持   | Default_2014-09-01 | MS-AZR-USGOV-0041P |
| **支持计划** | Azure 政府版专业直接支持 | Default_2014-09-01 | MS-AZR-USGOV-0042P |
| **支持计划** | Azure 政府版开发人员支持  | Default_2014-09-01 | MS-AZR-USGOV-0043P |

## <a name="determine-your-offer-type"></a>确定套餐类型
如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”  。 在服务列表中选择“订阅”。  在订阅列表菜单中，选择要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。   下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。 在生成发票之前估计所有成本。 显示的成本不包括免费额度和预付额度。

| **包括** | **不包括** |
| --- | --- |
| Azure 服务使用情况<sup>5</sup>        | 支持费用 - 有关详细信息，请参阅[发票条款说明](../understand/understand-invoice.md)。 |
| 市场产品/服务用量<sup>6</sup> | 税费 - 有关详细信息，请参阅[发票条款说明](../understand/understand-invoice.md)。 |
| 市场购买<sup>6</sup>      | 信用额度 - 有关详细信息，请参阅[发票条款说明](../understand/understand-invoice.md)。 |
| 预留项购买<sup>7</sup>      |  |
| 摊销预留项购买<sup>7</sup>      |  |

<sup>5</sup> Azure 服务用量基于预留价格和议价。 __

<sup>6</sup> 市场购买目前不适用于 MSDN 和 Visual Studio 套餐。 __

<sup>7</sup> 预留项购买目前仅适用于企业协议 (EA) 和 Microsoft 客户协议帐户。 __

## <a name="how-tags-are-used-in-cost-and-usage-data"></a>如何在成本和使用情况数据中使用标记

Azure 成本管理接收标记，作为各个服务提交的每个使用记录的一部分。 以下约束适用于这些标记：

- 必须直接将标记应用到资源，不能从父资源组隐式继承标记。
- 只有部署到资源组的资源才支持资源标记。
- 某些部署的资源可能不支持标记，或者可能在使用情况数据中不包含标记 - 请参阅 [Azure 资源的标记支持](../../azure-resource-manager/tag-support.md)。
- 应用标记时，资源标记只包含在使用情况数据中 - 标记不应用于历史数据。
- 资源标记只有在数据刷新后才可用于成本管理 - 请参阅[使用情况数据更新频率可变](#usage-data-update-frequency-varies)。
- 仅当资源处于活动/运行状态并生成使用情况记录时（例如，在 VM 不处于解除分配的情况下），资源标记才可用于成本管理。
- 若要管理标记，必须有每个资源的参与者访问权限。
- 若要管理标记策略，必须有管理组、订阅或资源组的所有者或策略参与者访问权限。
    
如果在成本管理中看不到特定标记，请考虑以下事项：

- 是否已将标记直接应用到资源？
- 标记的应用是否已超出 24 小时？ 请参阅[使用情况数据更新频率可变](#usage-data-update-frequency-varies)
- 资源类型是否支持标记？ 以下资源类型在 2019 年 12 月 1 日之前的使用情况数据中不支持标记。 如需所支持功能的完整列表，请参阅 [Azure 资源的标记支持](../../azure-resource-manager/tag-support.md)。
    - Azure Active Directory B2C 目录
    - Azure 防火墙
    - Azure NetApp 文件
    - 数据工厂
    - Databricks
    - 负载均衡器
    - 网络观察程序
    - 通知中心
    - 服务总线
    - 时序见解
    
下面是一些标记使用提示：

- 提前计划并定义标记策略，以便按组织、应用程序、环境等细分成本。
- 使用 Azure 策略将资源组标记复制到各个资源，并强制实施标记策略。
- 将标记 API 与 Query 或 UsageDetails 结合使用，根据当前标记获取所有成本。


## <a name="free-trial-to-pay-as-you-go-upgrade"></a>从免费试用到即用即付的升级

若要了解从免费试用版升级到即用即付定价后免费层服务的可用性，请参阅 [Azure 免费帐户常见问题解答](https://azure.microsoft.com/free/free-account-faq/)。

## <a name="rated-usage-data-refresh-schedule"></a>用量计费数据刷新计划

成本和用量数据在 Azure 门户上的“成本管理 + 计费”以及[支持 API](../index.yml) 中提供。 查看成本时，请记住以下几点：

- 当前计费周期的估算费用每天更新六次。
- 由于用量会增大，当前计费周期的估算费用可能会更改。
- 每次更新都是累积性的，包括前次更新的所有细目和信息。
- Azure 在当前计费周期结束后的最多 72 小时（三个日历日）内终结或_关闭_该计费周期。

以下示例演示计费周期的结束方式。

企业协议 (EA) 订阅 – 如果计费月在 3 月 31 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 4 月 4 日午夜 (UTC)。

即用即付订阅 – 如果计费月在 5 月 15 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新计费数据

无论是使用[成本管理 API](../index.yml)、Power BI 还是 Azure 门户检索数据，当前计费周期的费用预期都会重新计算，因此，在结算发票之前，此费用将会更改。

## <a name="cost-rounding"></a>成本舍入

成本管理中显示的成本是舍入的。 查询 API 返回的成本不舍入。 例如：

- Azure 门户中的成本分析 - 使用标准舍入规则对费用进行舍入：大于或等于 0.5 的值向上舍入，其余值向下舍入。 仅在显示值时才进行舍入。 在数据处理和聚合过程中不会发生舍入。 例如，成本分析会按如下所示聚合成本：
  -    费用 1：$0.004
  - 费用 2：$0.004
  -    得到的聚合费用：0.004 + 0.004 = 0.008。 显示的费用为 $0.01。
- 查询 API - 按 8 个小数位数显示费用，不舍入。


## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="historical-data-might-not-match-invoice"></a>历史数据可能与发票不匹配

基于额度的套餐和提前支付套餐的历史数据可能与发票不匹配。 某些 Azure 即用即付、MSDN 和 Visual Studio 套餐可将 Azure 额度和提前付款应用于发票。 但是，成本管理中显示的历史数据仅基于估计的消耗费用。 成本管理历史记录数据不包括付款和额度。 因此，针对以下套餐显示的历史数据可能不与发票完全匹配。

- 面向学生的 Azure (MS-AZR-0170P)
- Azure 开放许可 (MS-AZR-0111P)
- Azure Pass（MS-AZR-0120P、MS-AZR-0123P、MS-AZR-0125P、MS-AZR-0128P、MS-AZR-0129P）
- 免费试用版 (MS-AZR-0044P)
- MSDN (MS-AZR-0062P)
- Visual Studio（MS-AZR-0029P、MS-AZR-0059P、MS-AZR-0060P、MS-AZR-0063P、MS-AZR-0064P）

## <a name="see-also"></a>另请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](../../cost-management/quick-acm-cost-analysis.md)。
