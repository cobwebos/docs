---
title: 教程：在 Azure 门户中管理查询
description: 在本教程中，你会在 Azure 门户中创建 Resource Graph 查询并将这个新查询与其他人共享。
ms.date: 05/20/2020
ms.topic: tutorial
ms.openlocfilehash: e63a92a4570a0713ed65e1090e4d77d25fb10f95
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2020
ms.locfileid: "83637750"
---
# <a name="tutorial-create-and-share-an-azure-resource-graph-query-in-the-azure-portal"></a>教程：在 Azure 门户中创建和共享 Azure Resource Graph 查询

使用 Azure Resource Graph 资源管理器可以直接在 Azure 门户中保存 Resource Graph 查询。 查询有两种：_专用_和_共享_。 专用查询保存在 Azure 门户设置中。 而共享查询是资源管理器资源，可以使用基于角色的访问控制 (RBAC) 进行管理，并使用资源锁进行保护。 这两种类型的查询都是静态加密的。

通过将查询保存在 Azure 门户中，可以在查找收藏夹查询或常用查询时节省时间。 共享查询时，可以通过重复操作来帮助你的团队实现一致性和高效的目标。

在本教程中，你将完成以下任务：

> [!div class="checklist"]
> - 创建和删除专用查询
> - 创建共享查询
> - 发现共享查询
> - 删除共享查询

## <a name="prerequisites"></a>先决条件

需要一个 Azure 订阅才能完成此教程。 如果还没有该订阅，可以在开始前创建一个[免费帐户](https://azure.microsoft.com/free/)。

## <a name="create-and-delete-a-private-query"></a>创建和删除专用查询

专用查询仅供创建它们的帐户访问和可见。 由于这些查询保存在帐户的 Azure 门户设置中，因此只能在 Azure 门户中创建、使用和删除这些查询。 专用查询不是资源管理器资源。 若要创建新的专用查询，请执行以下步骤：

1. 从门户菜单中，选择“所有服务”  或使用所有页面顶部的 Azure 搜索框。 搜索并选择 Resource Graph 资源管理器  。

1. 在“Azure Resource Graph 资源管理器”页的“查询 1”  选项卡上，输入以下查询：

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```

   选择“运行查询”  以在底部窗格中查看查询结果。

   有关此查询的详细信息，请参阅[示例 – 按 OS 类型对虚拟机进行计数](../samples/starter.md#count-virtual-machines-by-os-type)。


1. 选择“保存”或“另存为”，输入“按 OS 对 VM 进行计数”作为名称，将类型保留为“专用查询”，然后选择“保存查询”窗格底部的“保存”       。 选项卡标题从“查询 1”更改为“按 OS 对 VM 进行计数”   。

1. 离开 Azure 门户中的 Azure Resource Graph 资源管理器，然后返回到该资源管理器。 请注意，不再显示已保存的查询，并且已返回“查询 1”选项卡  。

1. 选择“打开查询”  。 确保该类型为“专用查询”  。 已保存的名称“按 OS 对 VM 进行计数”  现在会显示在“查询名称”  列表中。 选择已保存查询的标题链接后，会将其加载到具有该查询的名称的新选项卡中。

   > [!NOTE] 
   > 当已保存的查询处于打开状态且选项卡显示其名称时，选择“保存”  按钮将使用所做的任何更改来更新它。 若要从这一打开的查询中创建新保存的查询，请选择“另存为”  并继续操作，就像保存全新的查询一样。

1. 若要删除已保存的查询，请再次选择“打开查询”  ，并验证“类型”  字段是否设置为“专用查询”  。 在已保存的 `Count VMs by OS` 查询的行上，选择“删除”  （回收站图标）。 在确认对话框中，选择“是”  以完成查询删除。
   然后，关闭“打开查询”  窗格。

## <a name="create-a-shared-query"></a>创建共享查询

与专用查询不同，共享查询是资源管理器资源。 这种情况意味着，这些查询保存到资源组，可以使用 RBAC 进行管理和控制，甚至可以使用资源锁进行保护。 作为资源，具有相应权限的任何人都可以查看和使用该资源。
若要创建新的共享查询，请执行以下步骤：

1. 从门户菜单中，选择“所有服务”  或使用所有页面顶部的 Azure 搜索框搜索并选择“Resource Graph 资源管理器”  。

1. 在“Azure Resource Graph 资源管理器”页的“查询 1”  选项卡上，输入以下查询：

   ```kusto
   Resources
   | where type =~ 'Microsoft.Compute/virtualMachines'
   | summarize count() by tostring(properties.storageProfile.osDisk.osType)
   ```
    
   选择“运行查询”  以在底部窗格中查看查询结果。

   有关此查询的详细信息，请参阅[示例 – 按 OS 类型对虚拟机进行计数](../samples/starter.md#count-virtual-machines-by-os-type)。

1. 选择“保存”或“另存为”   。

   
   ![使用“保存”按钮保存新查询](../media/create-share-query/save-shared-query-buttons.png)

1. 在“保存查询”  窗格中，输入“按 OS 对 VM 进行计数”作为名称  。

1. 将类型更改为“共享查询”  ，将描述设置为“按 OS 类型对虚拟机进行计数”  ，并将“订阅”  设置为指定查询资源的创建位置。

1. 选中“发布到 resource-graph-queries 资源组”复选框，并将“资源组位置”设置为“(美国)美国中西部”    。

1. 在“保存查询”窗格底部选择“保存”。   选项卡标题从“查询 1”更改为“按 OS 对 VM 进行计数”   。 第一次使用 resource-graph-queries 资源组时，创建资源组后保存所需的时间比预期时间更长  。
   
   ![将新查询另存为共享查询](../media/create-share-query/save-shared-query-window.png)

   > [!NOTE] 
   > 如果要提供保存共享查询的现有资源组的名称，则可以清除“发布到 resource-graph-queries 资源组”复选框  。 使用查询的默认命名资源组可以更轻松地发现共享查询。 它还使该资源组的用途更加明显。 但出于安全原因，可以根据现有权限选择现有资源组。

1. 离开 Azure 门户中的 Azure Resource Graph 资源管理器，然后返回到该资源管理器。 请注意，不再显示已保存的查询，并且已返回“查询 1”选项卡  。

1. 选择“打开查询”  。 验证类型是否设置为“共享查询”，以及“订阅”和“资源组”的组合是否与查询的保存位置匹配    。 已保存“按 OS 对 VM 进行计数”  项现在会显示在“查询名称”  列表中。 选择已保存查询的标题链接以将其加载到具有该查询的名称的新选项卡中。 作为共享查询，将在标题旁边的选项卡中显示一个图标，表示它是共享查询。

   ![显示带有图标的共享查询](../media/create-share-query/show-saved-shared-query.png)

   > [!NOTE] 
   > 当已保存的查询处于打开状态且选项卡显示其名称时，“保存”  按钮将使用所做的任何更改来更新它。 若要创建新保存的查询，请选择“另存为”  并继续操作，就像保存全新的查询一样。

## <a name="discover-shared-queries"></a>发现共享查询

由于共享查询是资源管理器资源，因此有多种方法可进行查找：

- 从 Resource Graph 资源管理器中，选择“打开查询”并将类型设置为“共享查询”   。
- 通过 Resource Graph 查询门户页。
- 通过保存共享查询的资源组。
- 通过查询 Resource Graph。

### <a name="view-resource-graph-queries"></a>查看 Resource Graph 查询

在 Azure 门户中，Resource Graph 查询页显示已登录帐户有权访问的共享查询。 此页支持按名称、订阅、资源组和 Resource Graph 查询的其他属性进行筛选。 还可以使用此界面标记、导出和删除 Resource Graph 查询。

选择其中一个查询将打开 Resource Graph 查询页。 与其他资源管理器资源一样，此页提供交互式概述以及活动日志、访问控制和标记。 还可以直接从此页应用资源锁。

通过从门户菜单中选择“所有服务”或使用所有页面顶部的 Azure 搜索框，转到 Resource Graph 查询页  。 搜索并选择 **Resource Graph Explorer**。

### <a name="list-resource-groups-resources"></a>列出资源组资源

Resource Graph 查询与作为资源组一部分的其他资源一起列出。
选择 Resource Graph 查询可打开该查询的页面。 省略号和快捷菜单选项（通过右键单击触发）的工作方式与 Resource Graph 查询页上的工作方式相同。

### <a name="query-resource-graph"></a>查询 Resource Graph

可以通过查询 Resource Graph 来查找 Resource Graph 查询。 以下 Resource Graph 查询按类型 `Microsoft.ResourceGraph/queries` 进行限制，然后使用 `project` 仅列出名称、修改时间和查询本身：

```kusto
Resources
| where type == "microsoft.resourcegraph/queries"
| project name, properties.timeModified, properties.query
```

## <a name="delete-a-shared-query"></a>删除共享查询

如果不再需要共享查询，请将其删除。 删除共享查询后，将删除相应的资源管理器资源。 结果图表固定到的任何仪表板现在都会显示错误消息。 显示该错误消息后，请使用“从仪表板中删除”  按钮清除仪表板。

可以通过以下界面删除共享查询：
- Resource Graph 查询页
- Resource Graph 查询页
- Resource Graph 资源管理器中的“打开查询”页 
- 资源组页

## <a name="clean-up-resources"></a>清理资源

完成本教程后，如果不再需要，请删除创建的专用查询和共享查询。

## <a name="next-steps"></a>后续步骤

在本教程中，你已创建专用查询和共享查询。 若要详细了解 Resource Graph 语言，请继续阅读查询语言详细信息页。

> [!div class="nextstepaction"]
> [获取有关查询语言的详细信息](../concepts/query-language.md)