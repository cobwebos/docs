---
title: 通过成本警报监视使用情况和支出 | Microsoft Docs
description: 本文介绍如何通过成本警报在 Azure 成本管理中监视使用情况和支出。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 05/21/2019
ms.topic: conceptual
ms.service: cost-management
manager: alavital
ms.custom: ''
ms.openlocfilehash: f1bf62596b6edcc6fff6572e431f3a777be93f05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/13/2019
ms.locfileid: "66002091"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>通过成本警报监视使用情况和支出

本文可帮助你了解和使用成本管理警报来监视 Azure 使用情况和支出。 使用 Azure 资源时，会自动生成成本警报。 使用警报时，可以在一个位置显示所有活动的成本管理和计费警报。 当使用量达到给定的阈值时，成本管理会生成警报。 有三种类型的成本警报：预算警报、信用额度警报、部门支出配额警报。

## <a name="budget-alerts"></a>预算警报

当基于使用情况或成本的支出达到或超出在[预算的警报条件](tutorial-acm-create-budgets.md)中定义的金额时，预算警报会通知你。 成本管理预算通过 Azure 门户或 [Azure 使用情况](https://docs.microsoft.com/rest/api/consumption) API 来创建。

在 Azure 门户中，预算按成本来定义。 使用 Azure 使用情况 API 时，预算是按成本或使用情况定义的。 预算警报支持基于成本的和基于使用情况的预算。 预算警报是在预算警报条件得到满足的情况下自动生成的。 可以在 Azure 门户中查看所有成本警报。 警报在生成后会显示在成本警报中。 此外，还会向预算的警报接收者列表中的人员发送警报电子邮件。

## <a name="credit-alerts"></a>信用额度警报

当 Azure 信用额度货币承诺用完以后，信用额度警报会通知你。 货币承诺适用于签署了企业协议的组织。 当 Azure 信用额度余额使用了 90% 和 100% 时，会自动生成信用额度警报。 警报在生成后会反映在成本警报中和发送给帐户所有者的电子邮件中。

## <a name="department-spending-quota-alerts"></a>部门支出配额警报

当部门支出达到配额的固定阈值时，部门支出配额警报会通知你。 支出配额在 EA 门户中配置。 阈值在达到后会生成一封给部门所有者的电子邮件并显示在成本警报中。 例如，配额的 50% 或 75%。

## <a name="supported-alert-features-by-offer-categories"></a>支持的产品/服务类别的警报功能

为警报类型的支持取决于有 (Microsoft 产品/服务) 的 Azure 帐户的类型。 下表显示了支持的警报功能的各种 Microsoft 产品。 您可以查看 Microsoft 产品/服务的完整列表[了解成本管理数据](understand-cost-mgt-data.md)。

| 警报类型 | 企业协议 | Microsoft 客户协议 | Web direct/付 As-You-进行 |
|---|---|---|---|
| 预算 | ✔ | ✔ | ✔ |
| 额度 | ✔ |✘ | ✘ |
| 部门支出配额 | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>查看成本警报

若要查看成本警报，可在 Azure 门户并选择中打开所需的范围**预算**菜单中。 使用**作用域**药丸切换到不同的作用域。 选择**成本警报**菜单中。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

![在成本管理中显示的警报的示例图像](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

活动警报和已消除警报的总数显示在成本警报页上。

所有警报都显示警报类型。 预算警报会显示其生成原因以及其适用的预算的名称。 每个警报会显示其生成日期、状态以及适用范围（订阅或管理组）。

可能的状态包括“活动”和“已消除”。   状态为“活动”表示警报仍然相关。 状态为“已消除”表示某人已通过标记的方式将警报设置为不再相关。

从列表中选择一个警报，查看其详细信息。 警报详细信息显示有关警报的详细信息。 预算警报包括一个预算链接。 如果提供了预算警报的建议，则也会显示该建议的链接。 预算、信用额度和部门支出配额警报在成本分析中有一个分析链接，用于浏览警报范围的成本。 以下示例显示某个部门的支出，其中包含警报详细信息。

![示例图像，显示某个部门的支出，其中包含警报详细信息](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

在查看已消除警报的详细信息时，如果需要手动操作，则可重新激活该警报。 下图显示了一个示例。

![示例图像，显示消除和重新激活选项](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>另请参阅

- 如果尚未创建预算或为预算设置警报条件，请完成[创建和管理预算](tutorial-acm-create-budgets.md)教程。
