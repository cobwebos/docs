---
title: 了解 Azure 成本管理数据 | Microsoft Docs
description: 本文帮助你更好地了解 Azure 成本管理中包含的数据、这些数据的处理频率以及收集、显示和关闭方式。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/07/2019
ms.topic: conceptual
ms.service: cost-management
manager: micflan
ms.custom: ''
ms.openlocfilehash: 1d7f58780819202eb02e3a48b4b5e0ac108a0432
ms.sourcegitcommit: 6cab3c44aaccbcc86ed5a2011761fa52aa5ee5fa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/20/2019
ms.locfileid: "56447457"
---
# <a name="understand-cost-management-data"></a>了解成本管理数据

本文帮助你更好地了解 Azure 成本管理中包含的数据。 此外，介绍这些数据的处理频率以及收集、显示和关闭方式。 你需要按月支付 Azure 使用费。 但是，月底的账单由 Azure 订阅类型决定。 成本管理接收用量数据的频率根据不同的因素而异。 此类因素包括处理数据所花费的时间，以及 Azure 服务向计费系统发出用量数据的频率。

## <a name="supported-microsoft-offers"></a>支持的 Microsoft 套餐

以下信息说明了 Azure 成本管理中目前支持的 [Microsoft Azure 套餐](https://azure.microsoft.com/support/legal/offer-details/)。  Azure 套餐是客户拥有的 Azure 订阅类型。

| 类别  | **产品/服务名称** | **套餐编号** |
| --- | --- | --- |
| **Azure 德国** | [Azure 德国即用即付](https://azure.microsoft.com/offers/ms-azr-de-0003p/) | MS-AZR-DE-0003P |
| **Azure Government** | Azure Government Enterprise | MS-AZR-USGOV-0017P |
| **企业协议 (EA)** | Enterprise 开发/测试 | MS-AZR-0148P |
| **企业协议 (EA)** | [Microsoft Azure Enterprise](https://azure.microsoft.com/offers/enterprise-agreement-support-upgrade/) | MS-AZR-0017P |
| **Microsoft 开发人员网络 (MSDN)** | [MSDN 平台](https://azure.microsoft.com/offers/ms-azr-0062p/) | MS-AZR-0062P |
| **即用即付** | [即用即付](https://azure.microsoft.com/offers/ms-azr-0003p/) | MS-AZR-0003P |
| **即用即付** | [即用即付开发/测试](https://azure.microsoft.com/offers/ms-azr-0023p/) | MS-AZR-0023P |
| **即用即付** | [Microsoft 合作伙伴网络](https://azure.microsoft.com/offers/ms-azr-0025p/) | MS-AZR-0025P |
| **即用即付** | [免费试用](https://azure.microsoft.com/offers/ms-azr-0044p/) | MS-AZR-0044P |
| **即用即付** | [Azure 开放式许可](https://azure.microsoft.com/offers/ms-azr-0111p/) | MS-AZR-0111P |
| **即用即付** | [面向学生的 Azure](https://azure.microsoft.com/offers/ms-azr-0170p/) | MS-AZR-0170P |
| **即用即付** | Azure Pass | MS-AZR-0120P、MS-AZR-0122P - MS-AZR-0125P、MS-AZR-0128P - MS-AZR-0130P |
| **Visual Studio** | [Visual Studio Enterprise – MPN](https://azure.microsoft.com/offers/ms-azr-0029p/) | MS-AZR-0029P |
| **Visual Studio** | [Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) | MS-AZR-0059P |
| **Visual Studio** | [Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) | MS-AZR-0060P |
| **Visual Studio** | [Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) | MS-AZR-0063P |
| **Visual Studio** | [Visual Studio Enterprise：BizSpark](https://azure.microsoft.com/offers/ms-azr-0064p/) | MS-AZR-0064P |

下表显示了不支持的套餐。

| 类别  | **产品/服务名称** | **套餐编号** |
| --- | --- | --- |
| **云解决方案提供商 (CSP)** | Microsoft Azure | MS-AZR-0145P |
| **云解决方案提供商 (CSP)** | Azure 政府版 CSP | MS-AZR-USGOV-0145P |
| **云解决方案提供商 (CSP)** | Microsoft 德国云 CSP 中的 Azure 德国版 | MS-AZR-DE-0145P |
| **即用即付** | 面向学生的 Azure 入门版 | MS-AZR-0144P |
| **即用即付** | [Microsoft Azure 赞助](https://azure.microsoft.com/offers/ms-azr-0036p/) | MS-AZR-0036P |
| **支持计划** | 标准支持 | MS-AZR-0041P |
| **支持计划** | 专业直接支持 | MS-AZR-0042P |
| **支持计划** | 开发人员支持 | MS-AZR-0043P |
| **支持计划** | 德国支持计划 | MS-AZR-DE-0043P |
| **支持计划** | Azure 政府版标准支持 | MS-AZR-USGOV-0041P |
| **支持计划** | Azure 政府版专业直接支持 | MS-AZR-USGOV-0042P |
| **支持计划** | Azure 政府版开发人员支持 | MS-AZR-USGOV-0043P |

对于使用即用即付、MSDN 和 Visual Studio 产品/服务类别的客户，从 2018 年 2 月 10 日起可在“成本管理”中使用数据。

如果未看到订阅数据并想要确定你的订阅是否属于受支持的套餐，可以验证你的订阅是否受支持。 若要验证 Azure 订阅是否受支持，请登录到 [Azure 门户](https://portal.azure.com)。 在左窗格中选择“所有服务”。 在服务列表中选择“订阅”。 在订阅列表菜单中，单击要验证的订阅。 该订阅将显示在“概述”选项卡上，在其中可以看到“套餐”和“套餐 ID”。 下图显示了一个示例。

![“订阅概述”选项卡的示例，其中显示了“套餐”和“套餐 ID”](./media/understand-cost-mgt-data/offer-and-offer-id.png)

## <a name="costs-included-in-cost-management"></a>成本管理中包括的成本

下表显示了成本管理中包括或不包括的数据。

**成本和用量数据**

| **包括** | **不包括** |
| --- | --- |
| Azure 服务用量<sup>1</sup> | 预订 – 有关详细信息，请参阅 [Azure 预订自动化的 API](../billing/billing-reservation-apis.md)。 |
| 市场套餐用量 | 市场购买 – 有关详细信息，请参阅[第三方服务费用](../billing/billing-understand-your-azure-marketplace-charges.md)。 |
|   | 支持费用 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
|   | 税费 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |
|   | 信用额度 - 有关详细信息，请参阅[发票条款说明](../billing/billing-understand-your-invoice.md)。 |

<sup>1</sup> Azure 服务用量基于预订价格和议价。

**元数据**

| **包括** | **不包括** |
| --- | --- |
| 资源标记<sup>2</sup> | 资源组标记 |

<sup>2</sup> 当每个服务发出用量数据并且这些数据不可用于追溯历史用量时，将应用资源标记。

## <a name="rated-usage-data-refresh-schedule"></a>用量计费数据刷新计划

成本和用量数据在 Azure 门户上的“成本管理 + 计费”以及[支持 API](https://aka.ms/costmgmt/docs) 中提供。 查看成本时，请记住以下几点：

- 当前计费周期的估算费用每天更新六次。
- 由于用量会增大，当前计费周期的估算费用可能会更改。
- 每次更新都是累积性的，包括前次更新的所有细目和信息。
- Azure 在当前计费周期结束后的最多 72 小时（三个日历日）内终结或_关闭_该计费周期。

以下示例演示计费周期的结束方式。

企业协议 (EA) 订阅 – 如果计费月在 3 月 31 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 4 月 4 日午夜 (UTC)。

即用即付订阅 – 如果计费月在 5 月 15 日结束，则在该日期后的最多 72 小时内更新估算费用。 在此示例中，更新截止时间为 5 月 19 日午夜 (UTC)。

### <a name="rerated-data"></a>重新计费数据

无论是使用[成本管理 API](https://aka.ms/costmgmt/docs)、PowerBI 还是 Azure 门户检索数据，当前计费周期的费用预期都会重新计算，因此，在结算发票之前，此费用将会更改。

## <a name="usage-data-update-frequency-varies"></a>用量数据的更新频率存在变化

成本管理中是否显示产生的用量数据取决于多种因素，包括：

- Azure 服务（例如存储、计算、CDN 和 SQL）发出用量数据的频率。
- 通过计费引擎和成本管理管道处理用量数据所花费的时间。

有些服务比其他服务更频繁地发出用量数据。 因此在成本管理中，某些服务的数据比发送频率更低的其他服务的数据更快出现。 一般情况下，服务用量数据需要在 8-24 小时后才会出现在成本管理中。 请注意，随着用量的增大，未结月份的数据将会更新，因为更新是累积性的。

## <a name="see-also"></a>另请参阅

- 如果尚未完成有关成本管理的第一个快速入门，请阅读[开始分析成本](quick-acm-cost-analysis.md)。
