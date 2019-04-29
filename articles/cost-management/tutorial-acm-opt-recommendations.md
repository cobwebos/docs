---
title: 教程 - 使用优化建议降低 Azure 成本 | Microsoft Docs
description: 本教程帮助你使用优化建议来降低 Azure 成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 03/13/2019
ms.topic: conceptual
ms.service: cost-management
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: 8471ae8ed0b391df11d81569b5660a2b098f5793
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2019
ms.locfileid: "61021340"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>教程：根据建议优化成本

Azure 成本管理与 Azure 顾问相结合，可以提供成本优化建议。 Azure 顾问通过识别闲置和未充分利用的资源来优化和提高效率。 本教程通过一个示例逐步讲解如何识别未充分利用的 Azure 资源，并采取措施来降低成本。

本教程介绍如何执行下列操作：

> [!div class="checklist"]
> * 查看成本优化建议，以查看潜在的低效使用情况。
> * 实施建议，以将虚拟机的大小调整为更具成本效益的选项
> * 验证操作，确保成功调整虚拟机的大小

## <a name="prerequisites"></a>必备组件
建议是可用于多个作用域和 Azure 帐户类型，包括[企业协议 (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)客户。 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 必须至少具有以下一个或多个范围的读取权限才能查看成本数据。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- 订阅
- 资源组

必须提供至少有 14 天保持活动状态的虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure
在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。

## <a name="view-cost-optimization-recommendations"></a>查看成本优化建议

若要查看订阅的成本优化建议，可在 Azure 门户并选择中打开所需的范围**顾问建议**。

若要查看管理组的建议，可在 Azure 门户并选择中打开所需的范围**成本分析**菜单中。 使用**作用域**药丸切换到不同的作用域，例如管理组。 选择**顾问建议**菜单中。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

![在 Azure 门户中显示的成本管理顾问建议](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

建议列表会指明低效使用情况，或显示可帮助你节省更多资金的采购建议。 “潜在年度节省”合计值显示在关闭或解除分配所有符合建议规则的 VM 的情况下，可以节省的总金额。 如果不想要关闭这些 VM，应考虑将其大小调整为费用更低的 VM SKU。

“影响”类别和“潜在年度节省”旨在帮助识别哪些建议有可能会实现最大的节省。 高影响的建议包括[购买虚拟机预留实例以节省超过即用即付成本的费用](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)和[通过调整大小或关闭未充分利用的实例来优化虚拟机开支](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)。 中等影响的建议包括[通过消减未预配的 ExpressRoute 线路来降低成本](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)和[通过删除或重新配置闲置虚拟网络网关来降低成本](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)。

## <a name="act-on-a-recommendation"></a>实施建议

Azure 顾问会监视虚拟机 14 天的使用情况，并识别利用不足的虚拟机。 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则会被视为利用率较低的虚拟机。

5% 或更低的 CPU 利用率设置是默认值，可以调整设置。 有关调整设置的详细信息，请参阅[配置平均 CPU 利用率规则或低使用率虚拟机建议](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)。

虽然某些方案有意使虚拟机利用率较低，但将虚拟机大小更改为费用更低的大小通常可以降低成本。 如果选择调整大小操作，实际节省的金额可能有所不同。 让我们演练一个调整虚拟机大小的示例。

在建议列表中，单击“适当调整大小或关闭未充分利用的虚拟机”建议。 在虚拟机候选项列表中，选择要调整大小的虚拟机并单击它。 随后会显示该虚拟机的详细信息，可在其中检查利用率指标。 “潜在年度节省”值是在关闭或删除该 VM 的情况下可以节省的金额。 调整 VM 大小也许能够节省一定的资金，但不能节省“潜在年度节省”中所列的全额。

![建议详细信息示例](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

在 VM 详细信息中，检查虚拟机的利用率，以确认它是否适合调整大小。

![示例 VM 详细信息显示了历史利用率](./media/tutorial-acm-opt-recommendations/vm-details.png)

注意当前虚拟机的大小。 确认应该调整该虚拟机的大小后，关闭 VM 详细信息以查看虚拟机列表。

在要关闭或调整大小的候选项列表中，选择“调整虚拟机大小”。
![具有调整虚拟机大小选项的示例建议](./media/tutorial-acm-opt-recommendations/resize-vm.png)

接下来，会看到可用调整大小选项的列表。 选择能够为方案实现最佳性能和成本效益的选项。 在以下示例中，所选的选项将 VM 大小从 **DS14\_V2** 调整为 **DS13\_V2**。 遵照建议可以节省 551.30 美元/月，或 6,615.60 美元/年。

![可以在其中选择一个大小的可用 VM 大小示例列表](./media/tutorial-acm-opt-recommendations/choose-size.png)

选择合适的大小后，单击“选择”开始执行调整大小的操作。

调整大小需要重启正在运行的虚拟机。 如果该虚拟机在生产环境中，则我们建议在非营业时间运行调整大小操作。 计划重启可以减少暂时性的中断造成的干扰。

## <a name="verify-the-action"></a>验证操作

成功完成 VM 大小调整后，会显示 Azure 通知。

![已成功调整大小的虚拟机通知](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了如何：

> [!div class="checklist"]
> * 查看成本优化建议，以查看潜在的低效使用情况。
> * 实施建议，以将虚拟机的大小调整为更具成本效益的选项
> * 验证操作，确保成功调整虚拟机的大小

请阅读“成本管理最佳做法”一文，其中提供了有助于管理成本的概要指导和原则。

> [!div class="nextstepaction"]
> [成本管理最佳做法](cost-mgt-best-practices.md)
