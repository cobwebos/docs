---
title: 查看对你重要的 Azure 顾问建议
description: 查看并筛选 Azure 顾问建议以减少干扰。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 10d7b16864f8e449dc51e870c5ff9f20d8c0dc87
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/25/2019
ms.locfileid: "75422372"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>查看对你重要的 Azure 顾问建议

Azure 顾问提供的建议可帮助你优化 Azure 部署。 在 Advisor 中，你有权访问一些功能，这些功能可帮助你将建议范围缩小为仅对你很重要的建议。

## <a name="configure-subscriptions-and-resource-groups"></a>配置订阅和资源组

顾问使你能够选择对你和你的组织至关重要的订阅和资源组。 你只会看到所选订阅和资源组的建议。 默认情况下，选择 "全部"。 配置设置应用于订阅或资源组，因此相同的设置适用于有权访问该订阅或资源组的每个人。 可以在 Azure 门户中或以编程方式更改配置设置。

若要在 Azure 门户中进行更改：

1. 在 Azure 门户中打开[Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 从菜单中选择 "**配置**"。

   ![Advisor 配置菜单](./media/view-recommendations/configuration.png)

1. 选中任何订阅或资源组的 "**包含**" 列中的框，以接收顾问建议。 如果该复选框处于禁用状态，则您可能没有在该订阅或资源组上进行配置更改的权限。 详细了解[Azure 顾问中的权限](permissions.md)。

1. 进行更改后，单击底部的 "**应用**"。

## <a name="filtering-your-view-in-the-azure-portal"></a>在 Azure 门户中筛选视图

配置设置保持活动状态，直到更改。 如果要限制单个查看的建议视图，可以使用 "顾问" 面板顶部提供的下拉。 在概述、高可用性、安全性、性能、成本和所有建议面板上，你可以选择要查看的订阅、资源类型和建议状态。

   ![Advisor 筛选菜单](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>消除和推迟建议

使用 Azure 顾问可以消除或推迟单个资源的建议。 如果关闭建议，除非手动激活，否则不会再次显示。 但是，推迟建议可以指定一个持续时间，在此之后，将再次自动激活建议。 延迟可以在 Azure 门户或以编程方式执行。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>推迟 Azure 门户中的单个建议 

1. 在 Azure 门户中打开[Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 选择建议类别以查看你的建议
1. 从建议列表中选择建议
1. 对于要推迟或消除的建议，请选择 "推迟" 或 "关闭"

     ![Advisor 筛选菜单](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>推迟或消除 Azure 门户中的多个建议

1. 在 Azure 门户中打开[Azure Advisor](https://aka.ms/azureadvisordashboard) 。
1. 选择建议类别，查看建议。
1. 从建议列表中选择一种建议。
1. 选中要推迟的所有资源的行左侧的复选框，或取消建议。
1. 在表的左上角选择 "**推迟**" 或 "**取消**"。

     ![Advisor 筛选菜单](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 你需要参与者或所有者权限来消除或推迟建议。 详细了解 Azure 顾问中的权限。

> [!NOTE]
> 如果禁用选择框，则可能仍在加载建议。 请等待所有建议加载，然后再尝试推迟或取消。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新激活推迟或消除的建议

可以激活已经推迟或消除的建议。 此操作可在 Azure 门户或以编程方式执行。 在 Azure 门户中：

1. 在 Azure 门户中打开[Azure Advisor](https://aka.ms/azureadvisordashboard) 。

1. 将概述面板上的筛选器更改为 "已**推迟**"。 然后，顾问将显示已延迟或已取消的建议。

    ![Advisor 筛选菜单](./media/view-recommendations/activate-postponed.png)

1. 选择一个类别以查看**推迟**和**消除**的建议。

1. 从建议列表中选择一种建议。 这会打开建议，其中已选择 "**延迟 & 消除**" 选项卡，以显示已延迟或消除此建议的资源。

1. 单击行末尾的 "**激活**"。 单击后，该资源的建议将处于活动状态，因此从此表中删除。 建议现在显示在 "**活动**" 选项卡中。
 
     ![Advisor 筛选菜单](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>后续步骤

本文介绍如何在 Azure 顾问中查看重要的建议。 若要详细了解顾问，请参阅以下资源： 

- [什么是 Azure 顾问？](advisor-overview.md)
- [与 Advisor 入门](advisor-get-started.md)
- [Azure Advisor 中的权限](permissions.md)



