---
title: 教程：使用锁保护新资源
description: 在本教程中，你将使用 Azure 蓝图资源锁选项“只读”和“不要删除”来保护新部署的资源。
ms.date: 05/06/2020
ms.topic: tutorial
ms.openlocfilehash: 90ffb0f5b8c1b6d3919b05abf778c5082bfee0dc
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864158"
---
# <a name="tutorial-protect-new-resources-with-azure-blueprints-resource-locks"></a>教程：使用 Azure 蓝图资源锁保护新资源

使用 Azure 蓝图[资源锁](../concepts/resource-locking.md)可以保护新部署的资源，防止其遭到篡改（即使使用拥有“所有者”角色的帐户，也无法篡改）。  可在蓝图定义中将这种保护添加到资源管理器模板项目创建的资源。

在本教程中，你将完成以下步骤：

> [!div class="checklist"]
> - 创建蓝图定义
> - 将蓝图定义标记为“已发布” 
> - 将蓝图定义分配到现有的订阅
> - 检查新资源组
> - 取消分配蓝图以删除锁

## <a name="prerequisites"></a>先决条件

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free)。

## <a name="create-a-blueprint-definition"></a>创建蓝图定义

首先创建蓝图定义。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧的“开始”页中，选择“创建蓝图”下的“创建”。   

1. 在页面顶部找到“空白蓝图”蓝图示例。  选择“以空白蓝图开始”。 

1. 在“基本信息”选项卡上输入此信息： 

   - **蓝图名称**：提供蓝图示例副本的名称。 本教程使用名称 **locked-storageaccount**。
   - **蓝图描述**：添加蓝图定义的说明。 使用“用于测试已部署资源中的蓝图资源锁定”。 
   - **定义位置**：选择省略号按钮 (...)，然后选择要将蓝图定义保存到的管理组或订阅。

1. 选择页面顶部的“项目”选项卡，或选择页面底部的“下一步:   项目”。

1. 添加订阅级别的资源组：
   1. 在“订阅”下选择“添加项目”行。  
   1. 在“项目类型”下选择“资源组”   。
   1. 将“项目显示名称”设置为 **RGtoLock**。 
   1. 将“资源组名称”  和“位置”  框保留为空，但请确保在每个属性上选中该复选框，以使其成为动态参数  。
   1. 选择“添加”  将此项目添加到蓝图中。

1. 在资源组下添加模板：
   1. 在“RGtoLock”条目下选择“添加项目”行   。
   1. 在“项目类型”下选择“Azure 资源管理器模板”，将“项目显示名称”设置为“StorageAccount”，并将“说明”保留为空      。
   1. 在“模板”  选项卡上，将以下资源管理器模板粘贴到编辑器框中。
      粘贴模板后，选择“添加”将此项目添加到蓝图。 

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
           "storageAccountName": "[concat('store', uniquestring(resourceGroup().id))]"
       },
       "resources": [{
           "type": "Microsoft.Storage/storageAccounts",
           "name": "[variables('storageAccountName')]",
           "location": "[resourceGroup().location]",
           "apiVersion": "2018-07-01",
           "sku": {
               "name": "[parameters('storageAccountType')]"
           },
           "kind": "StorageV2",
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

1. 选择页面底部的“保存草稿”  。

此步骤在选定的管理组或订阅中创建蓝图定义。

“保存蓝图定义成功”门户通知出现后，转到下一步骤。 

## <a name="publish-the-blueprint-definition"></a>发布蓝图定义

现已在环境中创建蓝图定义。 该副本在创建后处于“草稿”模式，必须先将其发布，然后才能分配和部署它。 

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到 **locked-storageaccount** 蓝图定义，并将其选中。

1. 选择页面顶部的“发布蓝图”。  在右侧的新窗格中，输入 **1.0** 作为**版本**。 以后做出更改时，此属性非常有用。 输入**更改注释**，例如，“为锁定蓝图部署的资源而发布的第一个版本”。  然后选择页面底部的“发布”。 

使用此步骤可将蓝图分配到订阅。 发布蓝图定义后，仍可进行更改。 如果进行了更改，则需要使用新的版本值发布定义，以跟踪同一蓝图定义的不同版本之间的差异。

“发布蓝图定义成功”门户通知出现后，转到下一步骤。 

## <a name="assign-the-blueprint-definition"></a>分配蓝图定义

发布蓝图定义后，可将它分配到它所在的管理组中的某个订阅。 在此步骤中，请提供参数来使蓝图定义的每个部署保持唯一。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“蓝图定义”页。  使用筛选器找到 **locked-storageaccount** 蓝图定义，并将其选中。

1. 选择蓝图定义页面顶部的“分配蓝图”。 

1. 提供蓝图分配的参数值：

   - **基础知识**

     - **订阅**：在蓝图定义所保存到的管理组中选择一个或多个订阅。 如果选择多个订阅，将使用输入的参数为每个订阅创建一个分配。
     - **分配名称**：系统会根据蓝图定义的名称预先填充该名称。 我们希望此分配表示新资源组的锁定，因此请将分配名称更改为 **assignment-locked-storageaccount-TestingBPLocks**。
     - **位置**：选择要在其中创建托管标识的区域。 Azure 蓝图使用此托管标识在分配的蓝图中部署所有项目。 若要了解详细信息，请参阅 [Azure 资源的托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。
       本教程选择了“美国东部 2”。 
     - **蓝图定义版本**：选择蓝图定义的已发布版本 **1.0**。

   - **锁分配**

     选择“只读”蓝图锁定模式。  有关更多信息，请参阅[蓝图资源锁定](../concepts/resource-locking.md)。

   - **托管的标识**

     使用默认选项：“系统分配”。  有关详细信息，请参阅[托管标识](../../../active-directory/managed-identities-azure-resources/overview.md)。

   - **项目参数**

     在本部分定义的参数将应用到定义了这些参数的项目。 这些参数属于[动态参数](../concepts/parameters.md#dynamic-parameters) ，因为它们是在分配蓝图期间定义的。 对于每个项目，请将参数值设置为“值”列中显示的值。 

     |项目名称|项目类型|参数名称|值|说明|
     |-|-|-|-|-|
     |RGtoLock 资源组|资源组|名称|TestingBPLocks|定义要将蓝图锁应用到的新资源组的名称。|
     |RGtoLock 资源组|资源组|位置|美国西部 2|定义要将蓝图锁应用到的新资源组的位置。|
     |StorageAccount|资源管理器模板|storageAccountType (StorageAccount)|Standard_GRS|存储 SKU。 默认值为 _Standard_LRS_。|

1. 输入所有参数后，选择页面底部的“分配”。 

此步骤部署定义的资源，并配置选定的**锁分配**。 应用蓝图锁最长可能需要花费 30 分钟。

“分配蓝图定义成功”门户通知出现后，转到下一步骤。 

## <a name="inspect-resources-deployed-by-the-assignment"></a>检查分配部署的资源

该分配创建了资源组 _TestingBPLocks_，资源管理器模板项目部署了存储帐户。 新资源组和选定的锁定状态显示在分配详细信息页上。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“分配的蓝图”页。  使用筛选器找到 **assignment-locked-storageaccount-TestingBPLocks** 蓝图分配，并将其选中。

   在此页中，可以看到分配成功消息和已部署资源的消息，以及新的蓝图锁定状态。 如果更新了分配，“分配操作”下拉列表会显示有关每个定义版本的部署的详细信息。  可以选择资源组打开属性页。

1. 选择“TestingBPLocks”资源组。 

1. 选择左侧的“访问控制(IAM)”  页。 然后选择“角色分配”选项卡  。

   在此处可以看到，_assignment-locked-storageaccount-TestingBPLocks_ 蓝图分配具有“所有者”角色。  之所以具有此角色，是因为资源组是使用此角色部署和锁定的。

1. 选择“拒绝分配”选项卡。 

   该蓝图分配在部署的资源组中创建了一个[拒绝分配](../../../role-based-access-control/deny-assignments.md)，以强制实施“只读”蓝图锁定模式。  该拒绝分配会阻止“角色分配”选项卡中具有相应权限的某人执行特定的操作。  拒绝分配会影响所有主体。 

   若要了解如何从拒绝分配中排除主体，请参阅[蓝图资源锁定](../concepts/resource-locking.md#exclude-a-principal-from-a-deny-assignment)。

1. 选择该拒绝分配，然后在左侧选择“拒绝的权限”页。 

   该拒绝分配正在阻止使用 **\*** 和 **Action** 配置的所有操作，但允许通过 **NotActions** 排除 **\*/read**，以此进行读取访问。

1. 在 Azure 门户痕迹导航中，选择“TestingBPLocks - 访问控制(IAM)”。  在左侧选择“概述”页，然后选择“删除资源组”按钮。   输入名称 **TestingBPLocks** 以确认删除，然后选择窗格底部的“删除”。 

   此时会显示门户通知“删除资源组 TestingBPLocks 失败”。  错误中指出，尽管你的帐户有权删除资源组，但蓝图分配拒绝了访问。 回顾前文，我们在蓝图分配期间选择了“只读”蓝图锁定模式。  蓝图锁会阻止具有权限的帐户（甚至包括“所有者”）删除资源。  有关更多信息，请参阅[蓝图资源锁定](../concepts/resource-locking.md)。

这些步骤演示了部署的资源现在受到蓝图锁的保护，蓝图锁可以阻止意外的删除，甚至可以阻止具有权限的帐户删除资源。

## <a name="unassign-the-blueprint"></a>取消分配蓝图

最后一步是删除蓝图定义的分配。 删除分配不会删除关联的项目。

1. 在左侧窗格中，选择“所有服务”  。 搜索并选择“蓝图”  。

1. 在左侧选择“分配的蓝图”页。  使用筛选器找到 **assignment-locked-storageaccount-TestingBPLocks** 蓝图分配，并将其选中。

1. 选择页面顶部的“取消分配蓝图”。  阅读确认对话框中的警告，然后选择“确定”。 

   删除蓝图分配时，蓝图锁也会一并删除。 具有相应权限的帐户现在又可以删除资源。

1. 在 Azure 菜单中选择“资源组”，然后选择“TestingBPLocks”。  

1. 在左侧选择“访问控制(IAM)”页，然后选择“角色分配”选项卡。  

资源组的安全性显示该蓝图分配不再拥有“所有者”访问权限。 

“删除蓝图分配成功”门户通知出现后，转到下一步骤。 

## <a name="clean-up-resources"></a>清理资源

完成本教程后，请删除以下资源：

- 资源组 _TestingBPLocks_
- 蓝图定义 _locked-storageaccount_

## <a name="next-steps"></a>后续步骤

在本教程中，你了解了如何保护使用 Azure 蓝图部署的新资源。 若要详细了解 Azure 蓝图，请继续学习蓝图生命周期文章。

> [!div class="nextstepaction"]
> [了解蓝图生命周期](../concepts/lifecycle.md)