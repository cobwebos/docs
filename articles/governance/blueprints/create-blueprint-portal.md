---
title: 在门户中创建 Azure 蓝图
description: 使用 Azure 蓝图创建、定义和部署项目。
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/18/2018
ms.topic: quickstart
ms.service: blueprints
manager: carmonm
ms.custom: mvc
ms.openlocfilehash: ca3f689ef91c0a1bcb751c78d4fa1d799ed3888b
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2018
ms.locfileid: "46984615"
---
# <a name="define-and-assign-an-azure-blueprint-in-the-portal"></a>在门户中定义和分配 Azure 蓝图

通过了解如何在 Azure 中创建和分配蓝图，组织可以定义一致性的常见模式，并根据资源管理器模板、策略、安全性等开发可重复使用和可快速部署的配置。 本教程介绍如何使用 Azure 蓝图来执行某些与在组织中创建、发布和分配蓝图相关的常见任务，例如：

> [!div class="checklist"]
> - 新建蓝图并添加各种受支持的项目
> - 对仍处于“草稿”状态的现有蓝图进行更改
> - 使用“已发布”将蓝图标记为分配就绪
> - 向现有订阅分配蓝图
> - 检查已分配蓝图的状态和进度
> - 删除已向订阅分配的蓝图

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-a-blueprint"></a>创建蓝图

定义符合性的标准模式的第一步是根据可用资源编写蓝图。 在此示例中，创建名为“MyBlueprint”的新蓝图，以配置订阅的角色和策略分配，添加新资源组，并在新资源组上创建资源管理器模板和角色分配。

1. 在 Azure 门户中单击“所有服务”，然后搜索并选择左侧窗格中的“策略”，启动 Azure 蓝图服务。 在“策略”页上，单击“蓝图”。

1. 从左侧页面选择“蓝图定义”，然后单击页面顶部的“+ 创建蓝图”按钮。

   - 或者，单击“入门”页上的“创建”，直接创建一个蓝图。

   ![创建蓝图](./media/create-blueprint-portal/create-blueprint-button.png)

1. 提供“蓝图名称”，例如“MyBlueprint”（字母和数字 - 最多 48 个字符，但不包含空格或特殊字符），但暂时将“蓝图说明”保留为空。  在“定义位置”复选框中，单击右侧的省略号，选择想要在其中保存蓝图的[管理组](../management-groups/overview.md)然后单击“选择”。

   > [!NOTE]
   > 蓝图定义只能保存到管理组。 若要创建第一个管理组，请按照[以下步骤](../management-groups/create.md)进行操作。

1. 验证信息是否正确（稍后无法更改“蓝图名称”和“定义位置”字段）并单击页面底部的“下一步: 项目”或页面顶部的“项目”选项卡。

1. 在订阅中添加角色分配：左键单击“订阅”下的“+ 添加项目...”行，随即会在浏览器右侧打开“添加项目”窗口。 为“项目类型”选择“角色分配”。 在“角色”下，选择“参与者”，保留“添加用户、应用或组”字段，并使用复选框指示动态参数。 单击“添加”将此项目添加到蓝图中。

   ![项目 - 角色分配](./media/create-blueprint-portal/add-role-assignment.png)

   > [!NOTE]
   > 大多数项目支持参数。 在蓝图创建期间为其分配值的参数是静态参数。 如果在蓝图分配期间分配参数，则该参数是动态参数。 有关更多信息，请参阅[蓝图参数](./concepts/parameters.md)。

1. 在订阅时添加策略分配：在“订阅”下直接左键单击“+ 添加项目...”行。 为“项目类型”选择“策略分配”。 将“类型”更改为“内置”，然后在“搜索”中输入“tag”。 单击“搜索”以进行筛选。 单击选择“对资源组应用标记及其默认值”。 单击“添加”将此项目添加到蓝图中。

1. 单击策略分配行“对资源组应用标记及其默认值”。 随即将打开作为蓝图定义一部分的向项目提供参数的窗口，并允许基于此蓝图而不是在分配期间（动态参数）设置所有分配的参数（静态参数）。 对于此示例，需要在蓝图分配期间使用动态参数，因此请保留默认值并单击“取消”。

1. 在订阅时添加资源组：左键单击“订阅”下的“+ 添加项目...”行。 为“项目类型”选择“资源组”。 将“资源组名称”和“位置”字段保留为空，但请确保在每个属性上选中该复选框，以使其成为动态参数。 单击“添加”将此项目添加到蓝图中。

1. 在资源组下添加模板：左键单击“+ 添加项目..” “ResourceGroup”条目下方的行。 为“项目类型”选择“Azure 资源管理器模板”，将“项目显示名称”设置为“StorageAccount”，并将“说明”保留为空。 在编辑器框的“模板”选项卡上，粘贴以下资源管理器模板。 粘贴模板后，单击“参数”选项卡，请注意模板参数“storageAccountType”默认值“Standard_LRS”已自动检测并填充，但配置为动态参数。 取消选中该复选框，并请注意，下拉列表仅包含 allowedValues 下的资源管理器模板中包含的值。 选中此框将其重新设置为动态参数。 单击“添加”将此项目添加到蓝图中。

   > [!IMPORTANT]
   > 如果导入模板，请确保该文件仅为 JSON 且不包含 HTML。 当指向 GitHub 上的 URL 时，请确保已单击“RAW”以获取纯 JSON 文件，而不是用 HTML 包装在 GitHub 上显示的文件。 如果导入的模板不是纯 JSON，将会出现错误。

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "storageAccountType": {
               "type": "string",
               "defaultValue": "Standard_LRS",
               "allowedValues": [
                   "Standard_LRS",
                   "Standard_GRS",
                   "Standard_ZRS",
                   "Premium_LRS"
               ],
               "metadata": {
                   "description": "Storage Account type"
               }
           }
       },
       "variables": {
           "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "apiVersion": "2016-01-01",
           "location": "[resourceGroup().location]",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "Storage",
           "properties": {}
       }],
       "outputs": {
           "storageAccountName": {
               "type": "string",
               "value": "[variables('storageAccountName')]"
           }
       }
   }
   ```

   ![项目 - 资源管理器模板](./media/create-blueprint-portal/add-resource-manager-template.png)

1. 已完成的蓝图应如下所示： 请注意，每个项目在“参数”列下都显示“已填充 y 个参数中的 x 个。 动态参数将在每次分配蓝图期间设置，并且已在角色分配上配置单个静态参数。

   ![已完成的蓝图](./media/create-blueprint-portal/completed-blueprint.png)

1. 现在已添加所有计划项目，请单击页面底部的“保存草稿”。

## <a name="edit-a-blueprint"></a>编辑蓝图

在[创建蓝图](#create_a_blueprint)中，未提供说明，也未将角色分配添加到新资源组。 可以通过执行以下步骤来解决上述问题：

1. 从左侧页面中选择“蓝图定义”。

1. 在蓝图列表中，右键单击之前创建的蓝图，然后选择“编辑蓝图”。

1. 在“蓝图说明”中，提供有关蓝图和组成它的项目的一些信息。  在这种情况下，输入如下内容：“此蓝图在订阅时设置标记策略和角色分配，创建 ResourceGroup，并将资源模板和角色分配部署到该 ResourceGroup。”

1. 单击页面底部的“下一步: 项目”或页面顶部的“项目”选项卡。

1. 在资源组下添加角色分配：左键单击“ResourceGroup”条目下方的“+ 添加项目...”行。 为“项目类型”选择“角色分配”。 在“角色”下，选择“所有者”并取消选中“添加用户、应用或组”字段，然后搜索并选择要添加的用户、应用或组。 这将是一个静态参数，并可用于此蓝图的每个分配。 单击“添加”将此项目添加到蓝图中。

   ![项目 - 角色分配 #2](./media/create-blueprint-portal/add-role-assignment-2.png)

1. 已完成的蓝图应如下所示： 请注意，新添加的角色分配显示“已填充 1 个参数中的 1 个”  ，这意味着它是一个静态参数。

   ![已完成的蓝图 #2](./media/create-blueprint-portal/completed-blueprint-2.png)

1. 现在既然草稿已更新，请单击“保存草稿”。

## <a name="publish-a-blueprint"></a>发布蓝图

现在，所有计划项目已添加到蓝图，就可以将其发布。
发布后，即可将其分配到订阅。

1. 从左侧页面中选择“蓝图定义”。

1. 在蓝图列表中，右键单击之前创建的蓝图，然后选择“发布蓝图”。

1. 在打开的对话框中，提供版本（字母、数字和连字符，最大长度为 20 个字符），例如“v1”；并提供更改注释（可选），例如“首次发布”。

1. 单击页面底部的“发布”。

## <a name="assign-a-blueprint"></a>分配蓝图

发布蓝图后，即可将其分配给订阅。 将你创建的蓝图分配给管理组层次结构中的一个订阅。

1. 从左侧页面中选择“蓝图定义”。

1. 在蓝图列表中，右键单击之前创建的蓝图（或左键单击省略号），然后选择“分配蓝图”。

1. 在“分配蓝图”页面上，从“订阅”下拉列表中选择要将此蓝图部署到的订阅。

   > [!NOTE]
   > 通过为所选的每个订阅创建一个分配，可以在以后对单个订阅分配进行更改，而不强制对所选订阅的其余部分进行更改。

1. 对于“分配名称”，请为此分配提供唯一名称。

1. 在“位置”中，选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../active-directory/managed-identities-azure-resources/overview.md)。

1. 在“v1”条目上保留已发布版本的蓝图定义版本下拉列表（默认为最近的已发布版本）。

1. 对于“锁定分配”，保留默认值“不锁定”。 有关更多信息，请参阅[蓝图资源锁定](./concepts/resource-locking.md)。

1. 对于订阅级别角色分配“[用户组或应用程序名称]：参与者”，搜索并选择用户、应用或组。

1. 对于订阅级别策略分配，请将“标记名称”设置为“CostCenter”，并将“标记值”设置为“ContosoIT”。

1. 对于“ResourceGroup”，从下拉列表中提供“StorageAccount”的名称和“East US 2”的位置。

   > [!NOTE]
   > 对于在蓝图定义期间在资源组下添加的每个项目，该项目将缩进以与将部署的资源组或对象对齐。 只有在上下文信息中才会列出无法获取参数或者在分配时没有要定义的参数的项目。

1. 在 Azure 资源管理器模板“StorageAccount”上，为 storageAccountType 参数选择“Standard_GRS”。

1. 阅读页面底部的信息框，然后单击“分配”。

## <a name="track-deployment-of-a-blueprint"></a>跟踪蓝图的部署

将蓝图分配给一个或多个订阅时，会发生以下两种情况：

- 将蓝图添加到分配给每个订阅的分配蓝图页面
- 开始部署蓝图定义的所有项目的过程

现在已将蓝图分配给订阅，请验证部署的进度。

1. 从左侧页面选择“分配的蓝图”。

1. 在蓝图列表中，右键单击之前分配的一个蓝图，然后选择“查看分配详细信息”。

   ![查看分配详细信息](./media/create-blueprint-portal/view-assignment-details.png)

1. 在“部署详细信息”页面上，验证是否已成功部署所有项目，以及在部署期间是否未出现任何错误。 如果发生错误，请参阅[蓝图疑难解答](./troubleshoot/general.md)，了解确定错误原因的操作步骤。

## <a name="unassign-a-blueprint"></a>取消分配蓝图

如果蓝图不再需要或由更新的蓝图替换为具有更新后的模式、策略和设计的蓝图时，可从订阅中将其删除。 删除蓝图，作为该蓝图的一部分分配的项目将保留。 要删除蓝图分配，请按照下列步骤操作：

1. 从左侧页面选择“分配的蓝图”。

1. 在蓝图列表中，选择要取消分配的蓝图，然后单击页面顶部的“取消分配蓝图”按钮。

1. 阅读确认消息，然后单击“确认”。

## <a name="delete-a-blueprint"></a>删除蓝图

1. 从左侧页面中选择“蓝图定义”。

1. 右键单击要删除的蓝图，然后选择“删除蓝图”，然后在确认对话框中单击“是”。

> [!NOTE]
> 删除此方法中的蓝图还将删除所选蓝图的所有“已发布版本”。 要删除单个版本，请打开蓝图，单击“已发布版本”选项卡，选择并单击要删除的版本，然后单击“删除此版本”。 此外，在删除所有蓝图分配之前，无法删除包含这些分配的蓝图。

## <a name="next-steps"></a>后续步骤

- 了解[蓝图生命周期](./concepts/lifecycle.md)
- 了解如何使用[静态和动态参数](./concepts/parameters.md)
- 了解如何自定义[蓝图排序顺序](./concepts/sequencing-order.md)
- 了解如何使用[蓝图资源锁定](./concepts/resource-locking.md)
- 了解如何[更新现有分配](./how-to/update-existing-assignments.md)
- 使用[常规疑难解答](./troubleshoot/general.md)在蓝图分配期间解决问题