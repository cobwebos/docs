---
title: Azure 成本管理概述 | Microsoft Docs
description: Azure 成本管理是一种成本管理解决方案，可帮助监视和控制 Azure 支出并优化资源使用。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 02/05/2018
ms.topic: overview
ms.service: cost-management
manager: benshy
ms.custom: ''
ms.openlocfilehash: e526d7fac3c66c5eaf9686db3c9f9f9506d6fc6b
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/06/2019
ms.locfileid: "55768649"
---
# <a name="what-is-azure-cost-management"></a>Azure 成本管理是什么？

成本管理是有效计划和控制业务所涉及成本的过程。 成本管理任务通常由财务、管理和应用等多个团队执行。 Azure 成本管理可以帮助组织在计划时考虑成本。 此外，它还有助于有效地分析成本并采取措施来优化云支出。 若要详细了解如何作为组织来处理成本管理，请参阅 [Azure 成本管理最佳做法](cost-mgt-best-practices.md)一文。

虽然与计费相关，但计费不等同于成本管理。 计费是为客户开具商品或服务的发票及管理商业关系的过程。  采购团队和财务团队通常负责执行计费任务。

成本管理会借助高级分析显示组织的成本和使用模式。 成本管理中的报表将显示 Azure 成本、使用情况、预留实例和 Azure 混合权益使用情况。 总的来说，这些报表显示了内部和外部使用成本以及 Azure 市场费用。 报告中尚未显示其他费用，例如预订购买费用、支持费用和税费。 报表可以帮助用户了解支出和资源使用情况，并帮助查找支出异常情况。 另外，还可以进行预测分析。 成本管理使用 Azure 管理组、预算和建议来清楚地显示费用规划情况，以及如何能够降低成本。

你可以使用 Azure 门户或各种 API 执行自动导出，从而将成本数据与外部系统和过程进行整合。 另外，还可自动导出计费数据和计划的报表。

## <a name="plan-and-control-expenses"></a>计划和控制支出

成本管理帮助你规划和控制成本的方式包括：成本分析、预算、建议和导出成本管理数据。

可以通过成本分析来了解和分析组织成本。 可以按组织查看合计成本，了解哪些方面持续产生成本并确定支出趋势。 此外，还可以查看一段时间的累计成本，根据预算预估每月、每季度，甚至每年的成本趋势。

预算可帮助你规划并满足组织中的财务责任。 它们有助于防止超出成本阈值或限制。 而且，预算还可以帮助你通知他人有关他们的支出，实现主动管理成本。 随着时间的推移，你可以看到支出的进展情况。

建议说明如何通过识别闲置和未充分利用的资源来优化和提高效率。 或者，它们可以显示价格更优惠的资源选项。 根据建议采取措施时，可以更改资源使用方式以节省资金。 为此，首先查看成本优化建议，以查看潜在的低效使用情况。 接下来，根据建议将 Azure 资源使用修改为更具成本效益的选择。 然后，验证操作以确保所做的更改成功。

如果你使用外部系统来访问或查看成本管理数据，则可以轻松地从 Azure 导出数据。 你可以设置一个每日计划导出，将数据文件以 CSV 格式导出并存储在 Azure 存储中。 然后，即可从外部系统访问数据。

## <a name="consider-cloudyn"></a>考虑使用 Cloudyn

[Cloudyn](overview.md) 是与成本管理相关的一项 Azure 服务。 使用 Cloudyn，你可以跟踪 Azure 资源的云使用情况和支出。 同时，它还支持其他云提供程序，包括 AWS 和 Google。 便于理解的仪表板报告也有助于成本分配和 Showback/Chargeback。 目前，成本管理不支持 Showback/Chargeback 或其他云服务提供程序。 但是，Cloudyn 支持这些提供程序。 目前，成本管理仅支持 Azure EA 帐户。 虽然它不支持个人或即用即付帐户或 Microsoft 云服务提供商帐户，但 Cloudyn 支持。 如果你拥有其中一个帐户，则可以使用 Cloudyn 来帮助管理成本。

## <a name="additional-azure-tools"></a>其他 Azure 工具

Azure 还包含其他一些工具，虽然这些工具不属于 Azure 成本管理功能集， 但它们在成本管理过程中都发挥着重要作用。 若要详细了解这些工具，请参阅以下链接。

- [Azure 定价计算器](https://azure.microsoft.com/pricing/calculator/) - 使用此工具可估计前期云成本。
- [Azure Migrate](../migrate/migrate-overview.md) - 评估当前数据中心的工作负载，获取有关 Azure 替代解决方案所需内容的见解。
- [Azure 顾问](../advisor/advisor-overview.md) - 识别未使用的 VM，并获得有关 Azure 预留实例购买的建议。
- [Azure 混合权益](https://azure.microsoft.com/pricing/hybrid-benefit/) - 使用当前用于 Azure 中 VM 的本地 Windows Server 或 SQL Server 许可证来实现成本节省。


## <a name="next-steps"></a>后续步骤

至此，你已熟悉成本管理，接下来将了解如何使用该服务。

- 开始使用 Azure 成本管理来[分析成本](quick-acm-cost-analysis.md)。
- 此外，还可以详细了解 [Azure 成本管理最佳做法](cost-mgt-best-practices.md)。
