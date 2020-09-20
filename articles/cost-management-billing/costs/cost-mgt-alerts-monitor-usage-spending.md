---
title: 通过成本警报监视使用情况和支出
description: 本文介绍如何通过成本警报在 Azure 成本管理中监视使用情况和支出。
author: bandersmsft
ms.author: banders
ms.date: 09/03/2020
ms.topic: how-to
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: adwise
ms.openlocfilehash: 72e9fd0d5a178897cf84b2babe4c02f7ef920841
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/15/2020
ms.locfileid: "90531332"
---
# <a name="use-cost-alerts-to-monitor-usage-and-spending"></a>通过成本警报监视使用情况和支出

本文可帮助你了解和使用成本管理警报来监视 Azure 使用情况和支出。 使用 Azure 资源时，会自动生成成本警报。 使用警报时，可以在一个位置显示所有活动的成本管理和计费警报。 当使用量达到给定的阈值时，成本管理会生成警报。 有三种类型的成本警报：预算警报、信用额度警报、部门支出配额警报。

## <a name="budget-alerts"></a>预算警报

当基于使用情况或成本的支出达到或超出在[预算的警报条件](tutorial-acm-create-budgets.md)中定义的金额时，预算警报会通知你。 成本管理预算通过 Azure 门户或 [Azure 使用情况](https://docs.microsoft.com/rest/api/consumption) API 来创建。

在 Azure 门户中，预算按成本来定义。 使用 Azure 使用情况 API 时，预算是按成本或使用情况定义的。 预算警报支持基于成本的和基于使用情况的预算。 预算警报是在预算警报条件得到满足的情况下自动生成的。 可以在 Azure 门户中查看所有成本警报。 警报在生成后会显示在成本警报中。 此外，还会向预算的警报接收者列表中的人员发送警报电子邮件。

可以使用预算 API 以其他语言发送电子邮件警报。 有关详细信息，请参阅[预算警报电子邮件支持的区域设置](manage-automation.md#supported-locales-for-budget-alert-emails)。

## <a name="credit-alerts"></a>额度警报

额度警报会在使用 Azure 额度货币承诺时通知你。 货币承诺适用于具有企业协议的组织。 在使用 90% 和 100% 的 Azure 额度余额时，会自动生成额度警报。 每当生成警报时，它都会反映在成本警报和发送给帐户所有者的电子邮件中。

## <a name="department-spending-quota-alerts"></a>部门支出配额警报

当部门支出达到固定配额阈值时，部门支出配额警报会通知你。 支出配额在 EA 门户中进行配置。 每当达到阈值时，它都会生成一封发送给部门所有者的电子邮件，并显示在成本警报中。 例如，达到配额的 50% 或 75% 时。

## <a name="supported-alert-features-by-offer-categories"></a>按产品/服务类别划分的受支持的警报功能

对警报类型的支持取决于你所拥有的 Azure 帐户类型（Microsoft 产品/服务）。 下表显示了各种 Microsoft 产品/服务支持的警报功能。 你可以在[了解成本管理数据](understand-cost-mgt-data.md)上查看 Microsoft 套餐的完整列表。

| 警报类型 | 企业协议 | Microsoft 客户协议 | Web 直接/即用即付 |
|---|---|---|---|
| 预算 | ✔ | ✔ | ✔ |
| 额度 | ✔ |✘ | ✘ |
| 部门支出配额 | ✔ | ✘ | ✘ |



## <a name="view-cost-alerts"></a>查看成本警报

要查看成本警报，请在 Azure 门户中打开所需范围，并在菜单中选择“预算”。 使用“范围”框切换到不同的范围。 在菜单中选择“成本警报”。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

![在成本管理中显示的警报的示例图像](./media/cost-mgt-alerts-monitor-usage-spending/budget-alerts-fullscreen.png)

活动警报和已消除的警报总数显示在成本警报页上。

所有警报都会显示警报类型。 预算警报会显示其生成原因以及其适用的预算的名称。 每个警报会显示其生成日期、状态以及适用范围（订阅或管理组）。

可能的状态包括“活动”和“已消除”。 “活动”状态指示警报仍相关。 “已消除”状态表示有人已对警报进行标记，设置为不再相关。

从列表中选择某个警报以便查看其详细信息。 警报详细信息显示有关警报的详细信息。 预算警报包括指向预算的链接。 如果某条建议可用于预算警报，则还会显示指向该建议的链接。 预算、额度和部门支出配额警报在成本分析中有一个分析链接，用于浏览警报范围的成本。 以下示例显示某个部门的支出，其中包含警报详细信息。

![示例图像，显示某个部门的支出，其中包含警报详细信息](./media/cost-mgt-alerts-monitor-usage-spending/dept-spending-selected-with-credits.png)

在查看已消除警报的详细信息时，如果需要手动操作，则可重新激活该警报。 下图显示了一个示例。

![示例图像，显示消除和重新激活选项](./media/cost-mgt-alerts-monitor-usage-spending/Dismiss-reactivate-options.png)

## <a name="see-also"></a>请参阅

- 如果尚未创建预算或为预算设置警报条件，请完成[创建和管理预算](tutorial-acm-create-budgets.md)教程。
