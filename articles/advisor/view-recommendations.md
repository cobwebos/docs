---
title: 查看对你重要的 Azure 顾问建议
description: 查看和筛选 Azure 顾问建议以减少干扰。
ms.topic: article
ms.date: 04/03/2019
ms.openlocfilehash: 43bdaba7d774bba8857a6eb3ef296d7ab8e1c264
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2020
ms.locfileid: "90986859"
---
# <a name="view-azure-advisor-recommendations-that-matter-to-you"></a>查看对你重要的 Azure 顾问建议

Azure 顾问提供的建议可用于优化 Azure 部署。 在 Azure 顾问中，你可以访问一些功能，以便将建议的范围缩小到那些对你来说重要的建议。

## <a name="configure-subscriptions-and-resource-groups"></a>配置订阅和资源组

可以通过 Azure 顾问选择对你和组织重要的订阅和资源组。 你只会看到所选的订阅和资源组的建议。 默认情况下，所有项都处于选中状态。 配置设置适用于订阅或资源组，因此相同的设置适用于可以访问该订阅或资源组的所有人。 可以通过 Azure 门户或编程方式更改配置设置。

若要通过 Azure 门户进行更改，请执行以下操作：

1. 在 Azure 门户中打开 [Azure 顾问](https://aka.ms/azureadvisordashboard)。

1. 在菜单中选择“配置”。

   ![Azure 顾问配置菜单](./media/view-recommendations/configuration.png)

1. 对于需要接收 Azure 顾问建议的订阅或资源组，请勾选“包括”列中的框。 如果禁用此框，则可能无权在该订阅或资源组上进行配置更改。 详细了解 [Azure 顾问中的权限](permissions.md)。

1. 进行更改后，单击底部的“应用”。

## <a name="filtering-your-view-in-the-azure-portal"></a>在 Azure 门户中筛选视图

配置设置在更改前保持活动状态 若要对建议视图进行限制以启用单一视图，可以使用在 Azure 顾问面板顶部提供的下拉列表。 在“概览”、“高可用性”、“安全性”、“性能”、“成本”、“所有建议”面板中，可以选择要查看的订阅、资源类型以及建议状态。

   :::image type="content" source="./media/view-recommendations/filtering.png" alt-text="显示筛选选项的 Azure 顾问的屏幕截图。":::

## <a name="dismissing-and-postponing-recommendations"></a>关闭和推迟建议

Azure 顾问允许关闭或推迟单个资源的建议。 如果关闭某个建议，则无法再次看到它，除非你手动激活它。 但是，推迟某个建议时，可以指定一个期限，该期限过后建议会自动再次激活。 可以通过 Azure 门户或编程方式进行推迟。

### <a name="postpone-a-single-recommendation-in-the-azure-portal"></a>在 Azure 门户中推迟单个建议 

1. 在 Azure 门户中打开 [Azure 顾问](https://aka.ms/azureadvisordashboard)。
1. 选择一个用于查看建议的建议类别
1. 选择建议列表中的建议
1. 针对要推迟或关闭的建议选择“推迟”或“关闭”

     :::image type="content" source="./media/view-recommendations/postpone-dismiss.png" alt-text="显示筛选选项的 Azure 顾问的屏幕截图。":::

### <a name="postpone-or-dismiss-a-multiple-recommendations-in-the-azure-portal"></a>在 Azure 门户中推迟或关闭多个建议

1. 在 Azure 门户中打开 [Azure 顾问](https://aka.ms/azureadvisordashboard)。
1. 选择一个用于查看建议的建议类别。
1. 选择建议列表中的建议。
1. 针对需要推迟或关闭建议的所有资源，选择行左侧的复选框。
1. 选择表左上的“推迟”或“关闭”。 

     :::image type="content" source="./media/view-recommendations/postpone-dismiss-multiple.png" alt-text="显示筛选选项的 Azure 顾问的屏幕截图。":::

> [!NOTE]
> 需要参与者或所有者权限才能关闭或推迟建议。 详细了解 Azure 顾问中的权限。

> [!NOTE]
> 在禁用选择框的情况下，建议可能仍在加载。 在尝试推迟或关闭之前，请等待所有建议加载完成。

### <a name="reactivate-a-postponed-or-dismissed-recommendation"></a>重新激活已推迟或关闭的建议

可以激活已推迟或关闭的建议。 可以通过 Azure 门户或编程方式完成此操作。 在 Azure 门户中：

1. 在 Azure 门户中打开 [Azure 顾问](https://aka.ms/azureadvisordashboard)。

1. 在“概览”面板上将筛选器更改为“已推迟”。 Azure 顾问然后会显示已推迟或关闭的建议。

    :::image type="content" source="./media/view-recommendations/activate-postponed.png" alt-text="显示筛选选项的 Azure 顾问的屏幕截图。":::

1. 选择一个用于查看“已推迟”或“已关闭”建议的类别。 

1. 选择建议列表中的建议。 这样就会打开“已推迟和已关闭”选项卡已处于选中状态的建议，显示已推迟或关闭此建议的资源。

1. 单击行尾的“激活”。 单击后，该资源的建议处于活动状态，并会从此表中删除。 现在，建议在“活动”选项卡中处于可见状态。
 
     :::image type="content" source="./media/view-recommendations/activate-postponed-2.png" alt-text="显示筛选选项的 Azure 顾问的屏幕截图。":::

## <a name="next-steps"></a>后续步骤

本文介绍了如何在 Azure 顾问中查看重要的建议。 若要详细了解顾问，请参阅以下资源： 

- [什么是 Azure 顾问？](advisor-overview.md)
- [Azure 顾问入门](advisor-get-started.md)
- [Azure 顾问中的权限](permissions.md)



