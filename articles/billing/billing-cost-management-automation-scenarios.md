---
title: Azure 计费和成本管理自动化方案 | Microsoft Docs
description: 了解如何将常用的计费和成本管理方案映射到不同的 API。
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: f84071577e9636e40d621093e2c57e3e9adf4913
ms.sourcegitcommit: 50f82f7682447245bebb229494591eb822a62038
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2018
ms.locfileid: "35247660"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>计费和成本管理自动化方案

常用的计费和成本管理空间方案已在下面标识，将映射到可以在这些方案中使用的不同 API。 在方案到 API 的映射下可以找到所有可用 API 及其提供的功能的摘要。 

## <a name="common-scenarios"></a>常见方案 

可以在多种方案中使用计费和成本管理 API 来解答与成本和使用情况相关的问题。  下面概述了常用方案。

- **发票对帐** - Microsoft 对我的收费是否正确？  我的帐单情况如何？我可以自行计算吗？

- **交叉收费** - 知道我的费用以后，需由组织中的谁来付费？

- **成本优化** - 我了解我的费用，但是，如何充分利用我在 Azure 的投资？

- **成本跟踪** - 我想要了解自己某段时间在 Azure 的开销和使用情况。 其趋势如何？ 如何进行改善？

- **当月的 Azure 开销** - 到目前为止，我当月的开销如何？ 我是否需要对自己的 Azure 开销和/或使用情况进行调整？ 这个月的哪个时间我使用 Azure 最多？

- **设置警报** - 我想要设置基于资源的使用量或基于资金的警报。

## <a name="scenario-to-api-mappings"></a>方案到 API 的映射

|         API/方案        | 发票对帐    | 交叉费用    | 成本优化    | 成本跟踪    | 月中支出    | 警报    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| 预算                     |                           |                  |           X          |                  |                    |     X     |
| 市场                |             X             |         X        |           X          |         X        |          X         |     X     |
| 价目表                 |             X             |         X        |           X          |         X        |          X         |           |
| 预留建议 |                           |                  |           X          |                  |                    |           |
| 预留详细信息         |                           |                  |           X          |         X        |                    |           |
| 预留摘要       |                           |                  |           X          |         X        |                    |           |
| 使用情况详细信息               |             X             |         X        |           X          |         X        |          X         |     X     |
| 计费周期             |             X             |         X        |           X          |         X        |                    |           |
| 发票                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| 未分级的使用情况               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> 下面的方案到 API 的映射不包括企业使用情况 API。 在将来，请尽可能利用常规使用情况 API 来解决全新开发方案的问题。

## <a name="api-summaries"></a>API 摘要

### <a name="consumption"></a>消耗
（Web Direct + 企业客户，适用于所有 API，下面指出的除外）

-   **预算**（仅企业客户）：使用[预算 API](https://docs.microsoft.com/rest/api/consumption/budgets) 创建资源、资源组或计费计量的成本或使用情况预算。  创建预算以后，即可配置警报，使之在用户定义的预算阈值超出以后进行通知。 也可将操作配置为在达到预算金额后发生。
-   **市场**：使用[市场费用 API](https://docs.microsoft.com/rest/api/consumption/marketplaces) 获取所有市场资源（Azure 第三方产品/服务）的费用和使用情况数据。 可以根据此数据来合计所有市场资源的成本，或者调查特定资源的成本/使用情况。
-   **价目表**（仅企业客户）：企业客户可以使用[价目表 API](https://docs.microsoft.com/rest/api/consumption/pricesheet) 检索所有计量的自定义定价。 企业可以将此数据与使用情况详细信息和市场使用情况信息结合起来，根据使用情况和市场数据来计算成本。 
-   **预留建议**：使用[预留建议 API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) 获取购买虚拟机预留实例的建议。 建议旨在让客户能够分析预期的成本节省情况和购买金额。
-   **预留详细信息**：使用[预留详细信息 API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) 查看以前购买的虚拟机预留的信息，例如预留使用量与实际使用量的对比。 可以按 VM 级别详细程度查看数据。
-   **预留摘要**：使用[预留摘要 API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) 可查看以前购买的虚拟机预留的聚合信息，例如聚合形式的预留使用量与实际使用量的对比。 
-   **使用情况详细信息**：通过[使用情况详细信息 API](https://docs.microsoft.com/rest/api/consumption/usagedetails) 获取所有 Azure 第一方资源的费用和使用情况。 信息采用使用情况详细信息记录的形式，这些记录目前会每天按计量发出。 可以根据信息来合计所有资源的成本，或者调查特定资源的成本/使用情况。
-   **RateCard**：Web Direct 客户可以使用 [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) 来获取其计量费率。 然后，他们可以使用返回的信息和资源使用情况信息来手动计算预期的帐单。 
-   **未分级的使用情况**：可以使用[未分级的使用情况 API](https://msdn.microsoft.com/library/azure/mt219003.aspx)，在 Azure 进行计量/收费之前获取原始的使用情况信息。

### <a name="billing"></a>计费
-   **计费周期**：使用[计费周期 API](https://docs.microsoft.com/rest/api/billing/billingperiods) 确定要分析的计费周期以及该周期的发票 ID。 发票 ID 可以与下面的发票 API 配合使用。 
-   **发票**：使用[发票 API](https://docs.microsoft.com/rest/api/billing/invoices) 获取给定计费周期的 PDF 格式发票的下载 URL。

### <a name="enterprise-consumption"></a>企业使用情况
（仅所有 API 企业）

-   **余额摘要**：使用[余额摘要 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) 获取关于余额、新购买、Azure 市场服务费用、调整和超额费用信息的每月摘要。 可以获取当前计费周期或过去的任何计费周期的此信息。 企业可以使用此数据，与手动计算的汇总费用进行比较。 此 API 不提供特定于资源的信息或成本的聚合视图。
-   **使用情况详细信息**：通过[使用情况详细信息 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) 获取当前月份、特定计费周期或自定义日期段的第一方 Azure 使用情况详细信息。 企业可以使用此数据根据费率和使用情况手动计算帐单，也可以根据提供的部门/组织信息来确定整个组织的成本归属情况。 可以根据这些数据按资源查看使用情况/成本。
-   **市场存储费用**：通过[市场存储费用 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) 获取当前月份、特定计费周期或自定义日期段的第三方 Azure 使用情况详细信息。 企业可以使用此数据根据费率和使用情况手动计算帐单，也可以根据提供的部门/组织信息来确定整个组织的成本归属情况。 可以通过市场存储费用 API 按资源查看使用情况/成本。
-   **价目表**：[价目表 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) 为给定注册和计费周期的每个计量提供适用的费率。 可以将此费率信息与使用情况详细信息和市场使用情况信息结合使用，以便手动计算预期的帐单。
-   **计费周期**：使用[计费周期 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) 获取一系列计费周期和一个属性，该属性指向的 API 路由针对四组与该计费周期相关的企业 API 数据：BalanceSummary、UsageDetails、市场费用、PriceSheet。
-   **预留实例建议**：[预留实例建议 API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) 可以查看客户 7 天、30 天或 60 天的虚拟机使用情况，提供单个和共享的购买建议。 预留实例 API 让客户能够分析预期的成本节省情况和建议的购买金额。

## <a name="next-steps"></a>后续步骤

- 若要了解如何使用 Azure 计费 API 以编程方式洞察 Azure 使用情况，请参阅 [Azure 计费 API 概述](billing-usage-rate-card-overview.md)。
