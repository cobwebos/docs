---
title: 查看对您重要的 Azure 顾问建议
description: 查看和筛选 Azure 顾问建议，以减少干扰。
services: advisor
author: kasparks
ms.service: advisor
ms.topic: article
ms.date: 04/03/2019
ms.author: kasparks
ms.openlocfilehash: 9f599a63fd5f52420f1b79e769d4f7bca9683b32
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2019
ms.locfileid: "59052832"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>查看对您重要的 Azure 顾问建议

Azure 顾问提供建议来帮助你优化 Azure 部署。 在顾问，您有权访问几个功能，可帮助您缩小为仅对您重要的建议。

## <a name="configure-subscriptions-and-resource-groups"></a>配置订阅和资源组

顾问使您能够选择订阅和对你和你的组织很重要的资源组。 您只看到建议的订阅和资源组的选择。 默认情况下，选中所有。 配置设置适用于订阅或资源组，因此相同的设置适用于每个人都有权访问该订阅或资源组。 在 Azure 门户中或以编程方式，可以更改配置设置。

若要在 Azure 门户中进行更改：

1. 打开[Azure 顾问](https://aka.ms/azureadvisordashboard)在 Azure 门户中。

1. 在菜单中选择“配置”。

   ![顾问配置菜单](./media/view-recommendations/configuration.png)

1. 中的复选框**Include**为任何订阅或资源组以接收顾问建议的列。 如果禁用了框中，您可能没有进行配置更改订阅或资源组上的权限。 详细了解如何[Azure 顾问中的权限](permissions.md)。

1. 单击**应用**底部后进行更改。

## <a name="filtering-your-view-in-the-azure-portal"></a>筛选 Azure 门户中的视图

配置设置保持活动状态，直到更改。 如果你想要限制为单个查看建议的视图，可以使用顶部的顾问面板提供相应的下拉列表。 从概述、 高可用性、 安全性、 性能、 成本和所有建议面板中，可以选择订阅、 资源类型和你想要查看的建议状态。

   ![顾问的筛选器菜单](./media/view-recommendations/filtering.png)

## <a name="dismissing-and-postponing-recommendations"></a>关闭和推迟建议

Azure 顾问，可取消或推迟单个资源的建议。 消除建议，如果您看不见它再次除非手动激活。 但是，推迟建议，可指定在其后建议自动重新激活持续时间。 推迟了可以在 Azure 门户中或以编程方式完成。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>推迟 Azure 门户中的单个建议 

1. 打开[Azure 顾问](https://aka.ms/azureadvisordashboard)在 Azure 门户中。
1. 选择一个建议类别并查看建议
1. 从建议列表中选择建议
1. 选择你想要推迟或消除的建议推迟或消除

     ![顾问的筛选器菜单](./media/view-recommendations/postpone-dismiss.png)

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>推迟或消除在 Azure 门户中的多个建议

1. 打开[Azure 顾问](https://aka.ms/azureadvisordashboard)在 Azure 门户中。
1. 选择一个建议类别并查看建议。
1. 从建议列表中选择建议。
1. 选择你想要推迟或消除该建议的所有资源所对应的行左侧的复选框。
1. 选择**推迟**或**解除**在左上角的表。

     ![顾问的筛选器菜单](./media/view-recommendations/postpone-dismiss-multiple.png)

> [!NOTE]
> 您需要关闭或推迟建议的参与者或所有者权限。 了解有关 Azure 顾问中的权限的详细信息。

> [!NOTE]
> 如果选择框处于禁用状态，可能仍会加载建议。 请等待所有建议尝试推迟或消除之前进行加载。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新激活已推迟或已消除的建议

您可以激活已推迟或消除的建议。 在 Azure 门户中或以编程方式，可以完成此操作。 在 Azure 门户中：

1. 打开[Azure 顾问](https://aka.ms/azureadvisordashboard)在 Azure 门户中。

1. 更改到概述面板上的筛选器**已推迟**。 顾问然后显示推迟或已消除的建议。

    ![顾问的筛选器菜单](./media/view-recommendations/activate-postponed.png)

1. 选择一个类别以查看**已推迟**并**已忽略**建议。

1. 从建议列表中选择建议。 这将打开与建议**推迟和解除**选项卡已被选中以显示为其已推迟或消除此建议的资源。

1. 单击**激活**的行的末尾。 单击之后，建议将处于该资源的活动状态且因此已删除此表中。 建议现已显示在**Active**选项卡。
 
     ![顾问的筛选器菜单](./media/view-recommendations/activate-postponed-2.png)

## <a name="next-steps"></a>后续步骤

本文介绍如何查看 Azure 顾问中对你重要的建议。 若要详细了解顾问，请参阅以下资源： 

- [什么是 Azure 顾问？](advisor-overview.md)
- [顾问入门](advisor-get-started.md)
- [Azure 顾问中的权限](permissions.md)



