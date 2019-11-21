---
title: 教程 - 使用优化建议降低 Azure 成本 | Microsoft Docs
description: 本教程帮助你使用优化建议来降低 Azure 成本。
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/24/2019
ms.topic: conceptual
ms.service: cost-management-billing
manager: dougeby
ms.custom: seodec18
ms.openlocfilehash: a9dbb121cab49024aaf0dc65bbac938764d9f8b2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229838"
---
# <a name="tutorial-optimize-costs-from-recommendations"></a>教程：通过建议优化成本

Azure 成本管理与 Azure 顾问相结合，可以提供成本优化建议。 Azure 顾问通过识别闲置和未充分利用的资源来优化和提高效率。 本教程通过一个示例逐步讲解如何识别未充分利用的 Azure 资源，并采取措施来降低成本。

本教程介绍如何执行以下操作：

> [!div class="checklist"]
> * 查看成本优化建议，以查看潜在的低效使用情况。
> * 实施建议，以将虚拟机的大小调整为更具成本效益的选项
> * 验证操作，确保成功调整虚拟机的大小

## <a name="prerequisites"></a>必备组件
Recommendations are available for a variety of scopes and Azure account types. 若要查看支持的帐户类型的完整列表，请参阅[了解成本管理数据](understand-cost-mgt-data.md)。 必须至少具有以下一个或多个范围的读取权限才能查看成本数据。 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

- Subscription
- Resource group

必须提供至少有 14 天保持活动状态的虚拟机。

## <a name="sign-in-to-azure"></a>登录 Azure
在 [https://portal.azure.com](https://portal.azure.com/) 中登录 Azure 门户。

## <a name="view-cost-optimization-recommendations"></a>查看成本优化建议

To view cost optimization recommendations for a subscription, open the desired scope in the Azure portal and select **Advisor recommendations**.

To view recommendations for a management group, open the desired scope in the Azure portal and select **Cost analysis** in the menu. Use the **Scope** pill to switch to a different scope, such as a management group. Select **Advisor recommendations** in the menu. 有关范围的详细信息，请参阅[了解并使用范围](understand-work-scopes.md)。

![在 Azure 门户中显示的成本管理顾问建议](./media/tutorial-acm-opt-recommendations/advisor-recommendations.png)

建议列表会指明低效使用情况，或显示可帮助你节省更多资金的采购建议。 “潜在年度节省”合计值显示在关闭或解除分配所有符合建议规则的 VM 的情况下，可以节省的总金额。 如果不想要关闭这些 VM，应考虑将其大小调整为费用更低的 VM SKU。

“影响”类别和“潜在年度节省”旨在帮助识别哪些建议有可能会实现最大的节省。

High impact recommendations include:
- [Buy reserved virtual machine instances to save money over pay-as-you-go costs](../advisor/advisor-cost-recommendations.md#buy-reserved-virtual-machine-instances-to-save-money-over-pay-as-you-go-costs)
- [Optimize virtual machine spend by resizing or shutting down underutilized instances](../advisor/advisor-cost-recommendations.md#optimize-virtual-machine-spend-by-resizing-or-shutting-down-underutilized-instances)
- [Use Standard Storage to store Managed Disks snapshots](../advisor/advisor-cost-recommendations.md#use-standard-snapshots-for-managed-disks)

Medium impact recommendations include:
- [Delete Azure Data Factory pipelines that are failing](../advisor/advisor-cost-recommendations.md#delete-azure-data-factory-pipelines-that-are-failing)
- [Reduce costs by eliminating un-provisioned ExpressRoute circuits](../advisor/advisor-cost-recommendations.md#reduce-costs-by-eliminating-unprovisioned-expressroute-circuits)
- [Reduce costs by deleting or reconfiguring idle virtual network gateways](../advisor/advisor-cost-recommendations.md#reduce-costs-by-deleting-or-reconfiguring-idle-virtual-network-gateways)

## <a name="act-on-a-recommendation"></a>实施建议

Azure Advisor monitors your virtual machine usage for seven days and then identifies underutilized virtual machines. 如果在 4 天或 4 天以上，虚拟机的 CPU 利用率都小于或等于 5% 且网络使用率小于或等于 7 MB，则会被视为利用率较低的虚拟机。

5% 或更低的 CPU 利用率设置是默认值，可以调整设置。 有关调整设置的详细信息，请参阅[配置平均 CPU 利用率规则或低使用率虚拟机建议](../advisor/advisor-get-started.md#configure-low-usage-vm-recommendation)。

虽然某些方案有意使虚拟机利用率较低，但将虚拟机大小更改为费用更低的大小通常可以降低成本。 如果选择调整大小操作，实际节省的金额可能有所不同。 让我们演练一个调整虚拟机大小的示例。

在建议列表中，单击“适当调整大小或关闭未充分利用的虚拟机”建议。 在虚拟机候选项列表中，选择要调整大小的虚拟机并单击它。 随后会显示该虚拟机的详细信息，可在其中检查利用率指标。 “潜在年度节省”值是在关闭或删除该 VM 的情况下可以节省的金额。 调整 VM 大小也许能够节省一定的资金，但不能节省“潜在年度节省”中所列的全额。

![建议详细信息示例](./media/tutorial-acm-opt-recommendations/recommendation-details.png)

在 VM 详细信息中，检查虚拟机的利用率，以确认它是否适合调整大小。

![示例 VM 详细信息显示了历史利用率](./media/tutorial-acm-opt-recommendations/vm-details.png)

注意当前虚拟机的大小。 确认应该调整该虚拟机的大小后，关闭 VM 详细信息以查看虚拟机列表。

In the list of candidates to shut down or resize, select **Resize *&lt;FromVirtualMachineSKU&gt;* to *&lt;ToVirtualMachineSKU&gt;***.
![具有调整虚拟机大小选项的示例建议](./media/tutorial-acm-opt-recommendations/resize-vm.png)

接下来，会看到可用调整大小选项的列表。 选择能够为方案实现最佳性能和成本效益的选项。 In the following example, the option chosen resizes from **Standard_D8s_v3** to **Standard_D2s_v3**.

![可以在其中选择一个大小的可用 VM 大小示例列表](./media/tutorial-acm-opt-recommendations/choose-size.png)

After you choose a suitable size, click **Resize** to start the resize action.

调整大小需要重启正在运行的虚拟机。 如果该虚拟机在生产环境中，则我们建议在非营业时间运行调整大小操作。 计划重启可以减少暂时性的中断造成的干扰。

## <a name="verify-the-action"></a>验证操作

成功完成 VM 大小调整后，会显示 Azure 通知。

![已成功调整大小的虚拟机通知](./media/tutorial-acm-opt-recommendations/resized-notification.png)

## <a name="next-steps"></a>后续步骤

本教程介绍了以下操作：

> [!div class="checklist"]
> * 查看成本优化建议，以查看潜在的低效使用情况。
> * 实施建议，以将虚拟机的大小调整为更具成本效益的选项
> * 验证操作，确保成功调整虚拟机的大小

请阅读“成本管理最佳做法”一文，其中提供了有助于管理成本的概要指导和原则。

> [!div class="nextstepaction"]
> [成本管理最佳做法](cost-mgt-best-practices.md)
